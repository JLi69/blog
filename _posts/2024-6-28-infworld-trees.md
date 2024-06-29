---
layout: post
title:  "Adding Trees to the OpenGL Terrain Demo"
---

![screenshot](/blog/images/infworld-tree.jpg)

In my [last post](https://jli69.github.io/blog/2024/06/24/computer-generated-trees.html) 
I wrote about generating trees with code and the screenshot above shows the
results from that being put into my ["infworld" demo](https://github.com/JLi69/infworld).

Overall, I really like how it looks and this post will go over some of the details
with implementing this feature.

# Tree Generation
I have the trees stored in this data structure that I call `DecorationTable`
because trees are "decorating" the landscape. The code for generating and placing
the trees in the world is shown below: 

{% highlight c++ %}
int getChunkSeed(int x, int z, const worldseed &permutations)
{
	//Taken from wikipedia
	//do some bit magic to hopefully ensure these values are not too periodic
	const unsigned w = 8 * sizeof(unsigned);
	const unsigned s = w / 2;
	unsigned a = x, b = z;
	a *= 3284157443; 
	b ^= a << s | a >> (w-s);
	b *= 1911520717; 
	a ^= b << s | b >> (w-s);
	a *= 2048419325;

	const rng::permutation256& p = permutations.at(0);
	return p[unsigned(p[unsigned(p[a % 256] + b) % 256] % 256)];
}

void DecorationTable::genDecorations(
	const worldseed &permutations,
	DecorationType type,
	unsigned int n,
	int x,
	int z,
	unsigned int index,
	std::minstd_rand0 &lcg
) {
	float chunksz = chunkscale * 2.0f * float(PREC) / float(PREC + 1);	
	float posx = float(z) * chunksz;
	float posz = float(x) * chunksz;
	unsigned int amount = (unsigned int)(lcg()) % n;

	for(int i = 0; i < amount; i++) {
		float x = float((unsigned int)(lcg()) % PREC) / float(PREC) - 0.5f;
		float z = float((unsigned int)(lcg()) % PREC) / float(PREC) - 0.5f;
		x *= chunksz;
		z *= chunksz;	
		x += posx;
		z += posz;
		float y = getHeight(z, x, permutations);	
		y *= HEIGHT;
		x *= float(PREC) / float(PREC + 1);
		z *= float(PREC) / float(PREC + 1);
		decorations.at(index).push_back({
			glm::vec3(x, y - 0.5f, z),
			type,
		});
	}
}

void DecorationTable::generate(const worldseed &permutations, unsigned int index)
{
	ChunkPos pos = positions.at(index);
	int seed = getChunkSeed(pos.x, pos.z, permutations);
	std::minstd_rand0 lcg;
	lcg.seed(seed);
	genDecorations(permutations, PINE_TREE, 72, pos.x, pos.z, index, lcg);
	genDecorations(permutations, TREE, 24, pos.x, pos.z, index, lcg);

	decorations.at(index).erase(std::remove_if(
		decorations.at(index).begin(),
		decorations.at(index).end(),
		[&permutations](Decoration d) {
			float x = d.position.x / 128.0f;
			float z = d.position.z / 128.0f;
			return perlin::noise(x, z, permutations.at(0)) < 0.0f;
		}
	), decorations.at(index).end());

	decorations.at(index).erase(std::remove_if(
		decorations.at(index).begin(),
		decorations.at(index).end(),
		[](Decoration d) {
			float y = d.position.y / HEIGHT;
			return d.type == TREE && (y < 0.02f || y > 0.2f);
		}
	), decorations.at(index).end());

	decorations.at(index).erase(std::remove_if(
		decorations.at(index).begin(),
		decorations.at(index).end(),
		[](Decoration d) {
			float y = d.position.y / HEIGHT;
			return d.type == PINE_TREE && (y < 0.04f || y > 0.3f);
		}
	), decorations.at(index).end());
}

//Generate decorations
void DecorationTable::genDecorations(const worldseed &permutations)
{
	for(int i = 0; i < decorations.size(); i++)
		generate(permutations, i);
}
{% endhighlight %}

Firstly, we have a function that assigns a seed value to each chunk based on
the permutation initially generated for creating the world and the chunk's
coordinates. This should allow for seemingly random but reproducable results.
This seed is then put into `std::minstd_rand0` which then generates the number
of decorations that should be placed in that chunk and then proceeds to generate
the positions of those "decorations". However, the program then filters the
results firstly by using a noise function to cull trees in certain areas so that
the trees are not too uniform and then removes trees that are at heights that
they should not be at (we don't want trees at the top of snow capped mountains
or at the bottom of the ocean, that's quite silly).

# Instancing
Initially I drew each tree one by one and had a draw call for every tree but that
proved to be rather inefficient and likely would not have scaled for a very large
number of trees. The problem with having a draw call for every tree is that for
each draw call, the CPU needs to send data to the GPU which is fairly time
consuming so limiting draw calls and attempting to group objects that need to
be drawn into one draw call would be more efficient. Since we only have two
tree models and therefore we are drawing a lot of objects that share the same
model, we can simply call `glDrawElementsInstanced` using instance buffers to
store the position of each tree. I found about instance buffers from this 
[learnopengl article](https://learnopengl.com/Advanced-OpenGL/Instancing)
and after some attempts, I was able to produce the following code to update and
generate the instance offset buffer:

{% highlight c++ %}
void DecorationTable::generateOffsets(
	DecorationType type,
	const gfx::Vao &vao,
	unsigned int minrange,
	unsigned int maxrange
) {
	if(decorations.size() == 0)
		return;

	std::vector<float> offsets;

	for(int i = 0; i < count(); i++) {
		ChunkPos pos = positions.at(i);

		if((labs(pos.x - centerx) < minrange &&
		    labs(pos.z - centerz) < minrange) ||
		   (labs(pos.x - centerx) >= maxrange ||
			labs(pos.z - centerz) >= maxrange))
			continue;

		for(const auto &decoration : decorations.at(i)) {
			if(decoration.type != type)
				continue;
			offsets.push_back(decoration.position.x * SCALE);
			offsets.push_back(decoration.position.y * SCALE);
			offsets.push_back(decoration.position.z * SCALE);
		}
	}

	if(vaoCount.count(vao.vaoid))
		vaoCount.at(vao.vaoid) = offsets.size() / 3;
	else
		vaoCount.insert({ vao.vaoid, offsets.size() / 3 });

	glBindBuffer(GL_ARRAY_BUFFER, vao.buffers.at(4));
	glBufferData(GL_ARRAY_BUFFER, sizeof(float) * offsets.size(), &offsets[0], GL_STATIC_DRAW);
	glBindBuffer(GL_ARRAY_BUFFER, 0);
}
{% endhighlight %}

while doing this however, I ended up having to come across perhaps one of the
stupidest bugs that I have ever encountered. Originally, I only had the following
code for updating `vaoCount` (`vaoCount` is simply an unordered map that has
the vao id as a key and the number of instances that need to be drawn as the
corresponding value):

{% highlight c++ %}
vaoCount.insert({ vao.vaoid, offsets.size() / 3 });
{% endhighlight %}

I thought that `insert` would update the value at the key if the key already
existed but that was not the case. I spent a little bit too much time trying to
figure out why there were all of a sudden giant gaps that would appear and
disappear whenever the program would generate new trees (it happened because
the vao count was not updated but there might now be more vaos to draw and
therefore the program was drawing less instances than it should have).

Updating the code to be the following ended up making it work:
{% highlight c++ %}
if(vaoCount.count(vao.vaoid))
	vaoCount.at(vao.vaoid) = offsets.size() / 3;
else
	vaoCount.insert({ vao.vaoid, offsets.size() / 3 });
{% endhighlight %}

Overall, instancing has very good performance but it seems that now the infworld
demo consumes quite a lot of the integrated GPU on my laptop and while by itself
it runs at a mostly smooth 59-60 FPS, when it has to share computing power with
a process (like Firefox playing a YouTube video) it seems to suffer frame drops
sometimes.

Some other features I implemented were lower detailed tree models so that trees
farther away would be drawn with less vertices and therefore consume less resources
and allow for more trees to be drawn and I also set limits on how far the trees
would be rendered.

Anyway, my only real issue with adding trees is the performance hit that comes
with having to render so many models and I might be able to make some improvements
if I really tried (like reducing the vertices from one of the tree models with
has about 1.4k vertices for the high detail model and ~400 for the low detail
model) but after spending almost two? three? (I checked the git log, I started
this project on April 1st) months on this, I am getting a little tired.
However, I am happy with the current features of this demo and 
I am ready to move on to another project (although I likely will be looking to 
reuse the code from this project).

You can download the executable for Windows and Linux 
[here](https://github.com/JLi69/infworld/releases/tag/demo2.1).

Anyway, that's it, have a nice day.

__EDIT 2024-6-29:__ Okay, it turns out I was a little stupid and found some bugs
in the original demo 2 (you can still download the bugged version 
[here](https://github.com/JLi69/infworld/releases/tag/demo2)) but I updated it
and hopefully those bugs are now squashed.
