---
layout: post
title:  "OpenGL Terrain Demo: Infinite Terrain"
---

In the [previous post](https://jli69.github.io/blog/2024/04/19/opengl-terrain-demo.html)
I described my process for creating a demo that displayed some procedural terrain
using OpenGL. Since then, I have worked on it a little more and I have published
its source code onto [github](https://github.com/JLi69/infworld). If you want to
check it out, I have also created a release with a demo version. 

However, the version that I had wrote about on April 19th had one limitation:
once you generated a world the program would not generate any more terrain once
you travelled further leading to an edge of the world:

![screenshot](/blog/images/infinite-terrain-img1.png)

Of course, as hinted by the fact that I titled the project *infworld* I wanted
to implement infinite terrain generation and in this post, I will give the quick
details on how I implemented it.

# Implementing Infinite Terrain Generation
All of the chunk vertex buffer object ids and vertex array object ids are managed
by the following data structure:

{% highlight c++ %}

class ChunkTable {
	unsigned int chunkcount;
	unsigned int size;
	float chunkscale;
	float height;
	std::vector<unsigned int> vaoids;
	std::vector<unsigned int> bufferids; 
	std::vector<ChunkPos> chunkpos;
	int centerx = 0, centerz = 0;

	//For generating new chunks
	std::vector<unsigned int> indices;
	std::vector<ChunkPos> newChunks;
public:
	ChunkTable(unsigned int range, float scale, float h);
	void genBuffers();
	void clearBuffers();
	void addChunk(
		unsigned int index,
		const mesh::ElementArrayBuffer<float> &chunkmesh,
		int x,
		int z
	);
	void addChunk(unsigned int index, const ChunkData &chunk);
	void bindVao(unsigned int index);
	ChunkPos getPos(unsigned int index);
	unsigned int count() const;
	ChunkPos getCenter();
	void setCenter(int x, int z);
	void generateNewChunks(
		float camerax,
		float cameraz,
		const worldseed &permutations
	);
};

{% endhighlight %}

Upon starting up the program, chunks are generated around `(0, 0)` and the
camera will start at `(0, 0)` as well with the 'center chunk' (the chunk that
the camera is supposed to be in and all chunks that will be rendered to the screen 
are centered around). Every frame, the function `generateNewChunks` will be 
given the camera x and z coordinates and then determine whether the camera is
still within the center chunk. If it still is, the function returns, and otherwise
it will attempt to decide which chunks can be discarded and determine what
new chunks should be generated to replace those chunks that were discarded.

The code for the function `generateNewChunks` follows:

{% highlight c++ %}

void ChunkTable::generateNewChunks(
	float camerax,
	float cameraz,
	const worldseed &permutations
) {
	if(indices.size() > 0) {
		int i = indices.size() - 1;
		int x = newChunks.at(i).x, z = newChunks.at(i).z;
		ChunkData chunk = buildChunk(permutations, x, z, height, chunkscale);
		addChunk(indices.at(i), chunk);
		indices.pop_back();
		newChunks.pop_back();
		return;
	}

	int
		ix = int(floorf((cameraz + chunkscale * SCALE) / (chunkscale * SCALE * 2.0f))),
		iz = int(floorf((camerax + chunkscale * SCALE) / (chunkscale * SCALE * 2.0f)));
	if(ix == centerx && iz == centerz)
		return;

	int range = (size - 1) / 2;
	for(int x = ix - range; x <= ix + range; x++) {
		for(int z = iz - range; z <= iz + range; z++) {
			if(labs(x - centerx) <= range && labs(z - centerz) <= range)
				continue;
			newChunks.push_back({ x, z });
		}
	}

	//Determine which chunks are out of range
	for(int i = 0; i < chunkcount; i++) {
		int 
			chunkx = chunkpos.at(i).x,
			chunkz = chunkpos.at(i).z;	
		if(labs(ix - chunkx) <= range && labs(iz - chunkz) <= range)
			continue;
		indices.push_back(i);
	}	

	centerx = ix;
	centerz = iz;
}

{% endhighlight %}

The first part of the function checks if the list of indices of chunks that
are now out of viewing range is nonempty and then replaces that chunk with a
new chunk. One chunk is generated per frame so that you don't notice a stutter
when you cross chunk borders and the fog ends up obscuring the generation
anyway so you wouldn't notice it happening. Additionally, the function returns
after generating one new chunk so that it will not bother beginning the process
to generate more new chunks in order to avoid something like a race condition
as I set up the new chunk generation to effectively be sort of concurrent and
I found that if you crossed a chunk border at a corner of a chunk (essentially
you went into a new chunk and then immediately went into another new chunk) then
the program would not have finished generating new chunks and therefore it would
screw up the lists of chunks that needed to be generated and to avoid that I
just added that return. I haven't noticed anything appear to be wrong so it seems
to mostly work.

If we make it past that portion of the function, we then check if the camera is
outside of the center chunk and if the camera is indeed outside, then it will
check every chunk to see if it is out of viewing range and mark it to be
replaced. Afterwards, we then create a list of new chunk positions to be put
into the noise function to be generated. These two lists should in theory be
the exact same size.

There are a couple limitations to this method of infinite terrain generation:
firstly it does not keep the chunks in a nice order meaning that you wanted to
search for a chunk at a specific location (to modify perhaps) then you would
need to search the entire list of chunks. I could likely just shift around
the chunks in the list of vertex buffer ids and vertex array ids as those values
are only unsigned integers and therefore are easy to move. However, I'm lazy and
I don't see a good reason to do that right now so I'm not going to implement that.
However, if you wanted to create an application where you wanted to actually modify
the chunks in the terrain (like a Minecraft clone) then you would probably would
want to keep the chunks in some kind of order.

# Other stuff

Here are some other small changes that I made to the program:

#### Multithreading chunk generation

Initially generating the world was rather slow in the beginning so I made it so
that the program would count the number of threads available on the machine and
then attempt to generate a chunk on each thread simultaneously which I found
sped up the initial generation by about four times on an laptop with eight
threads.

#### Skybox

Staring at a blank blue background was rather bland so I learned how to use
a cubemap from a learnopengl [tutorial](https://learnopengl.com/Advanced-OpenGL/Cubemaps)
and downloaded a cubemap from [OpenGameArt](https://opengameart.org/content/sky-box-sunny-day)
to make a nice skybox.

#### Command line arguments

I added a couple command line arguments to provide input to the program without
having to recompile it every time I wanted to make a change to a constant.

Here's a quick summary of the two command line arguments I added:

`-r` or `--range`:
This essentially controls the viewing distance in terms of how many chunks
are visible, the default is 10 but you can set it to be anywhere between 3 and
64\. Note that increasing this value will necessarily lead to lower performance.

`-s` or `--seed`:
This allows you to set a specific seed for a world so that you can generate the
same terrain if you find something cool.

Examples:

{% highlight sh %}
# (sets the viewing range to be 24 chunks and the seed to be 123)
./infworld -r 24 -s 123
# (more verbose version)
./infworld --range 24 --seed 123
# just set the viewing range to be 24, have a random seed
./infworld -r 24
# set the seed to be 123 but have a viewing distance of 10
./infworld -s 123
{% endhighlight %}

# Cool images

seed: 919989363 (viewing range is 24)

![screenshot](/blog/images/infinite-terrain-img2.png)

seed: 123

Pushing my laptop to its absolute limits by setting the viewing range to be
64 - it's runing at a nice and toasty 17-20 frames per second.

![screenshot](/blog/images/infinite-terrain-img3.png)


what seed 0 looks like:

![screenshot](/blog/images/infinite-terrain-img4.png)

Anyway, you can download the demo [here](https://github.com/JLi69/infworld/releases/tag/demo)
if you want to check it out. Enjoy!

Note: I'm still experimenting with how the terrain generation looks so expect
potential changes in the future.
