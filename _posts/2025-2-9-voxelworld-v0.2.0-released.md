---
layout: post
title:  "Voxelworld v0.2.0 is now released!"
---

![screenshot](/blog/images/voxelworld-v0.2.0-1.jpg)

A small sample of the new blocks added in this update.

![screenshot](/blog/images/voxelworld-v0.2.0-2.jpg)

A screenshot of all the slabs in the block selection menu.

Alright, I suppose that this is the first post of year. After about a month of
work, I have managed to implement all the features that I wanted to get done for
the `v0.2.0` update to Voxelworld. My main goal with this update was to add in a
bunch of blocks and also expand support in the voxel engine for different block
geometries that aren't necessarily perfect cubes.

One of the features that I added was that pretty much any solid block can be
turned into a slab or a stair - blocks consist of two bytes of data (the block
id and the block geometry data) and since I stored block geometry data seperate
to the block id, that means that I don't really need to put in too much effort
for the creation of new stairs or slabs. It also gets you weird items such as
grass block slabs and coal ore stairs.

I've also updated the world save format to instead save chunks into large
"region" files that can allow for bulk loading of chunks (so that way I don't
have to query the filesystem as much or as often in order to load new chunks)
which seems to have made chunk loading faster. Additionally, chunks are now
smaller at as size of 16 x 16 x 16 (previously they had been 32 x 32 x 32) so
that remeshing is faster. The only downside to this is that any world created
in `v0.1.*` is no longer compatible. However, considering that only affects
two (alpha) versions I don't think it's that big of a deal.

I'm not going to go into too much detail about everything that I've done over
the past month but my plan for `v0.3.0` for Voxelworld is to implement some kind
of lighting engine so that should be a fun programming challenge.

Check out the source code [here](https://github.com/JLi69/voxelworld).

Download the game on [itch.io](https://nullptr-error.itch.io/voxelworld).

Anyway that's it, have a nice day!
