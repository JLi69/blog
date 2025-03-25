---
layout: post
title:  "Implementing a Lighting Engine for Voxelworld"
---

![screenshot](/blog/images/voxelworld-lightingengine1.jpg)

A single torch at night.

![screenshot](/blog/images/voxelworld-lightingengine2.jpg)

A cave with a blue and green torch.

![screenshot](/blog/images/voxelworld-lightingengine3.jpg)

Some lava.

![screenshot](/blog/images/voxelworld-lightingengine4.jpg)

An entrance to a creepy dark cave.

My main focus for `v0.3.0` for [Voxelworld](https://nullptr-error.itch.io/voxelworld)
has been to implement a very basic lighting engine. The lighting engine is not
going to be that fancy - there's not going to be things like ambient occlusion or
smooth lighting so in that aspect it is inferior to Minecraft's lighting engine.
I might implement those features in the future when I have time and motivation.
But for now, I just implemented something similar to what Minecraft lighting was
like in the Alpha edition.

One bonus that this lighting engine has over Minecraft's lighting engine is that
it supports colored lighting (so you could have things like red, green, and
blue torches). I made the lava emit orange light and I really like how it looks
in the caves.

Light is not stored in the world save, it's just generated on the fly when you
load into a world or when you generate new chunks. The performance is passable on
my laptop (at least most of the time, sometimes there are noticeable lag spikes)
for a render distance of 7 chunks. It's definitely worse than other voxel games like
Minecraft and Minetest (I think it's called Luanti now but I'm still calling it
Minetest) but I think it'll be fine for now. Adding colored lighting was actually
not that difficult, all I had to do was store each channel seperately and propagate
each channel's light values individually. Light propagation is actually surprisingly
similar to the [water physics](https://www.youtube.com/watch?v=tL8i6KDHpWA) except
for the fact that light is not affected by gravity. Essentially it's just a BFS from
a light source to the rest of the world.

Sky light was a little more difficult to implement, Voxelworld uses cubic chunks
which means that some chunks above the player are not loaded and those unloaded
chunks might contain blocks that would affect sky light levels in the loaded
chunks. I considered many ways of dealing with this but ultimately I decided
that almost all of the time anyone using this program would likely not end up
running into this issue since most of the chunks above should be completely empty.
I just chose the simple but technically not 100% correct option of just generating
sky light on the fly when the world is loaded and when new chunks are generated/loaded.
This could lead to some potentially strange results in some extreme edge cases but
I feel that those are manageable and it works fine enough for now.

Another thing of note about sky light is that sky light does not attenuate downwards
when it has a level of 15 but the moment it becomes <= 14, it starts to attenuate
in all directions. I believe that this technically is not how Minecraft does it but
I've decided to keep it in as it looks fine enough.

Also, another thing to note about the lighting engine is that there might be room
for optimizations, particularly those relating to generating new light in newly
loaded chunks (especially with sky light). However, I think for now it performs
well enough and I'm debating releasing `v0.3.0` sometime in the near future.
The next goal would be to figure out some more optimizations in things like
chunk rendering (so as to increase the render distance) and also begin
implementing things like survival mode.

If you would like to try out the game or test out the lighting engine, go to
[Voxelworld's itch.io page](https://nullptr-error.itch.io/voxelworld) and
consider giving it a shot!

Anyway, that's it, have a nice day.

![screenshot](/blog/images/voxelworld-dynlighting.jpg)

By the way, I implemented dynamic lighting - if you're holding an item that emits
light it will light up the surroundings. It does not affect the light levels of
the blocks but it's a nice visual effect. The screenshot shows this with the player
holding a torch.
