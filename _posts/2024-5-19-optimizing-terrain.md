---
layout: post
title:  "OpenGL Terrain Demo: Optimizations"
---

![screenshot](/blog/images/terrain-optimization1.jpg)

In my [last post](https://jli69.github.io/blog/2024/05/07/infinite-terrain.html) 
on this project I implemented infinite terrain generation but unfortunately I
could not render too much terrain due to performance (I believe that on my laptop
it started dropping below 60 frames per second around a render distance of 20-24
chunks). However, I wanted to make the terrain renderer faster and more capable
of showing a greater amount of terrain at 60 FPS so in this post I will discuss
some of the optimizations that I made.

# Frustum culling
The first optimization that I decided to implement was frustum culling, where
I have the program calculate the view frustum that represents the entire space
that the camera can see and then determine if a chunk is outside of said frustum,
then I skip over rendering it. This allows us to make significantly less draw
calls which improves performance. Admittedly this is a rather common optimization
for most video games but it is quite effective at improving performance with
not too much effort. After implementing this, the frames per second when
rendering 64 chunks of render distance went from 17-20 FPS to about 30-34 FPS,
which is almost a double improvement and I was able to go up to 32 chunks of
render distance without going below 55-60 FPS.

# Level of Detail (LOD)
Another optimization that I implemented was reducing the level of detail of
the terrain for far away terrain. This is also a common optimization in most
games and while I could learn how to use something like a tesselation shader,
I decided to keep it somewhat simple for now and instead I chose to just have
a `ChunkTable` array where the `ChunkTable` at index 0 would have he highest
level of detail by having the chunks be of the smallest scale and then from
there doubled the size of each chunk for the other chunktables in the array.
I got this idea from this [video](https://www.youtube.com/watch?v=5zlfJW2VGLM) 
by Vercidium (I also used his video as a guide for implementing some other 
optimizations that I described in the first post that I made in this series on
terrain rendering in OpenGL). I then also made it so that any low detail chunks
that were entirely within a region of higher quality chunks would be skipped
over when rendering so that you did not see any low quality terrain close up
while also slightly improving performance.

The following is a screenshot with the range of the highest quality terrain
being 16 chunks (the default is now 8):

![screenshot](/blog/images/terrain-optimization2.jpg)

While this proved to be quite effective optimization (probably why it is so
common in video games) the way I implemented it resulted in artifacts where
different regions of different LOD met. I tried to minimize how much these
regions intersected but that simply resulted in more undesirable artifacts such
as cracks in the terrain. I eventually gave up trying to solve these artifacts
for the time being as currently they aren't particularly noticable, at least if
you don't look too closely. However, you can probably still notice low quality
terrain turning into higher quality terrain and high quality terrain turning into
low quality terrain but I decided that would be an acceptable visual effect for
now. I might figure out some ways to eliminate or at least better hide any
visual artifacts in the future.

# Optimizing Water Rendering
Okay, so this was rather odd optimization that I made but for some reason whenever
you would point the camera at a large body of water the program would slow down
to 50-55 frames per second. For a while I didn't realize what was happening
until I realized that it likely was something to do with the fact that I was
passing three seperate textures to the water shader (two for normal maps and the
other for a dudv map) and apparently that was not playing nicely with the GPU
cache or something (I don't know the exact details) but I do remember when I
started this project and I was texturing terrain I was passing 4 different
textures to the terrain fragment shader which was significantly slowing down
performance and when I merged all those textures into a single one, it resulted
in much better performance. I merged the dudv map and the normal maps into a
single texture and it actually solved the problem! Now hopefully whenever you 
look at the water it shouldn't have too much of an effect on the FPS. The way
I sampled from this texture did result in some seams in the water though which 
is rather unfortunate but I made it so that those seams weren't too visible
unless you were very close to the water and the water still looks decent from
a distance so I consider some small seams in the water worth it if it means
a significant improvement to peformance.

# Conclusion
Anyway, that's pretty much a summary of what I have been working on with this
project over the past week or so. I still want to add some more details to the
terrain (such as trees) and also possibly eliminate some visual artifacts so that
is likely what I'm going to be working on next.

That's all I have to say, have a nice day!
