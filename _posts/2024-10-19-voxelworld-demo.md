---
layout: post
title: "\"Voxelworld\": First Demo! (A Minecraft clone written in the Rust Programming Language)"
---

![screenshot](/blog/images/voxelworld-demo1.jpg)

![screenshot](/blog/images/voxelworld-demo2.jpg)

![screenshot](/blog/images/voxelworld-demo3.jpg)

Hello!

So over the past month I have been working on a Minecraft-like voxel engine using
the Rust programming language. The images you see above are some examples of what the
program currently looks like. Note that it is still very much a work in progress
and progress may be a little slow considering I now have school filling up my
schedule. However, I do intend on continuing the project over some amount of time.

The code likely isn't the best and there are definitely some improvements I can
probably make but overall I think Voxelworld is looking quite nice right now.
The source code is released under the GPLv3 license and you browse it
[here](https://github.com/JLi69/voxelworld). Additionally, if you want to contribute
I welcome any pull requests and suggestions you might make (I might come up with
a more formal way of managing contributions in the future if enough people do
contribute but that's a bridge to cross on a later day as Voxelworld is nowhere
near big or popular enough).

Anyway, this post will document some of the more interesting aspects of the
technical side of implementing a voxel engine so enjoy!

# Chunk Mesh Generation
Of course, like any voxel engine, any face that is hidden by another block
is not included in the mesh. Additionally, to reduce the amount of data being
sent to the GPU, instead of using 32 bit floating point numbers to represent
vertices, we instead pass 8 bit integers to represent vertex position. These
positions are all relative to the chunk so in the shader the vertices need to
be transformed to their appropriate location in world space. Additionally, there's
another 8 bits to represent the texture and 8 bits to carry other data that might
be associated with the vertex (though at the moment it only has 2 bits allocated
to represent the direction the face is pointing in - the x, y, z axes). In total,
each vertex is only 40 bits or 5 bytes in size (comparing this to a previous 
Minecraft Clone I worked on - [blockgame](https://github.com/JLi69/blockgame),
which had 5 floating point values for a single vertex - x, y, z and texture
coordinates, for a total of 160 bits or 20 bytes per vertex, it's quite an
improvement).

Additionally, Voxelworld uses cubic chunks which allows for an infinite build
height. Moreover, to reduce the number of draw calls made, the program culls
any chunks that are not intersecting the view frustum.

# World generation

Currently world generation isn't very sophisticated, I just used a crate
called [noise](https://crates.io/crates/noise) and used their built-in fractal
perlin noise to generate the terrain and called it a day. Nothing too fancy. I
intend on adding some more features to the terrain later as it currently looks
rather bland with only some hills and valleys.

Also, worlds generate infinitely and only the chunks within the player's render
distance are loaded.

# World saves

This is something that I actually did not include in my previous "Minecraft-clone"
[blockgame](https://github.com/JLi69/blockgame), so it was fun to figure out.
Chunks are stored in a file titled `chnk_x_y_z` where x, y, z represent the
chunk's coordinates in the world. To save space, the chunks are run-length encoded
(I have a 16 bit integer telling the length of a strech of consecutive
indentical blocks and then the block data is stored with it). This is fairly
effective as a lot of chunks contain identical blocks (some are completely empty
and contain only air) it seems that worlds only take up a few megabytes when first
generated.

Anyway, that's about it for what I've been working on. I hope to make some
improvements within the near future though things might get busy with schoolwork
so development might be a little slow. My immediate next goal is to improve world
generation to include caves and trees so that should hopefully be fun to figure
out.

Anyway, that's it, have a nice day!

