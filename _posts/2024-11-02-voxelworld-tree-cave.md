---
layout: post
title:  "Voxelworld: Adding Cave and Tree Generation"
---

![screenshot](/blog/images/voxelworld-tree-cave1.jpg)

![screenshot](/blog/images/voxelworld-tree-cave2.jpg)

![screenshot](/blog/images/voxelworld-tree-cave3.jpg)

![screenshot](/blog/images/voxelworld-tree-cave4.jpg)

Alright, so over the past couple of weeks I've been working on improving
world generation for Voxelworld and we now have tree and cave generation!
I personally like how it looks, though I think adjustments can still be made.

Other than tree and cave generation however, I didn't really add that much
so this post will be a little short.

# Cave generation

The cave generation isn't particularly complicated - I just used 3D perlin noise
to "carve" out the caves and the caves get bigger as you go deeper down. It's
not the same as Minecraft which for any version <= 1.17 uses carver caves to
ensure the caves are somewhat well connected (caves generated with only noise
tend to not be well connected) and any version >= 1.18 uses noise caves as well.
However, from what I've seen from some test worlds, it seems that the caves lower
in the world are decently connected so I think cave generation works fine for
now. Perhaps later I can figure out how to implement carver cave generation?
That would be cool.

# Tree generation

Tree generation isn't too complicated either. For each chunk the game
generates a list of (x, z) positions that a tree is at and also generates
potential positions the tree is at in neighboring chunks so that way
any trees at the edges of chunks aren't cut off and will be instead generated
in the neighboring chunk.

There are definitely some things to improve but it works for now which I'm
happy with.

Anyway, that's it, have a nice day!
