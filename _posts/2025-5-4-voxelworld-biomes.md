---
layout: post
title:  "Adding Biomes to Voxelworld/Other Terrain Generation Upgrades"
---

Recently I've added biomes and made some upgrades to the terrain generation
of [Voxelworld](https://github.com/JLi69/voxelworld). You can check out a demo
video [on Youtube](https://www.youtube.com/watch?v=flRk_HFk9Z4). Don't worry,
the old world generation is still available as "Old" style generation.

I've mostly made two changes: updating terrain generation and adding biomes.

## Terrain Generation

![Voxelworld mountain](/blog/images/voxelworld-mountain.jpg)

_A large mountain._

Terrain generation now has three parameters: base elevation, elevation, and
steepness. Elevation is added onto the base height based on steepness, if steepness
is close to 0, not much is added, and if it is closer to 1, then more is added.
This creates the potential for some hilly regions, steep mountains, and flat
plains at varying heights (allowing for oceans if base height and steepness
are low enough). Another independent noise function determines mountain height,
if the mountain height exceeds the terrain height by a sufficient amount, the
terrain turns from grass into stone and if the mountain generates high enough,
then the surface block turns into snow. One interesting quirk about the mountain
generation is that sometimes the mountains don't generate that high above the
terrain so you will end up seeing the occasional "stone hill" which looks a bit
odd but I like them so I intend on keeping them in the game.

All of this combined seems to create a fairly diverse set of terrain features.

## Biomes

![Voxelworld snow biome](/blog/images/voxelworld-snow-biome.jpg)

_A snow/arctic biome._

![Voxelworld desert biome](/blog/images/voxelworld-desert-biome.jpg)

_A desert biome._

The main parameter that controls biomes is temperature: if the temperature is
below 0.25, then it is an "arctic" biome, and if the temperature is above 0.75,
then it is a desert biome. Arctic biomes have snow and ice generates on top of
bodies of water. This does create an interesting effect where if an arctic biome
generates above a large body of water (an ocean if you will), it creates an
"icy ocean" which is kind of fun. In deserts, the ground is made up sand and you
can find cacti and dead bushes.

There is also a tree generation noise function which determines how many trees
should generate in an area, this creates areas with high densities of trees
(like forests) and areas with few/no trees (kind of like plains).

A thing to note is that mountains will override whatever biome generated in a
location.

This added on top of the terrain generation creates for some nice looking and
diverse worlds, which I like a lot.

## Conclusion

![Voxelworld mountain view](/blog/images/voxelworld-mountain-view.jpg)

_A nice view from a mountain._

Anyway, this post was a quick update on the development of Voxelworld.
One of the issues that I currently have with the new generation though is that
it can be rather laggy at times so I am trying to think of ways I can optimize
it. Once I manage to get that sorted out, another feature that I want to add for
the `v0.4.0` update for Voxelworld is a survival mode test, which I think would
be a lot of fun.

Anyway, that's it, have a nice day!
