---
layout: post
title:  "Voxelworld v0.4.0 is now released!"
---

![screenshot](/blog/images/voxelworld-v0.4.0.jpg)

A screenshot of a simple starter base.

After quite a few months of work, Voxelworld `v0.4.0` is finally finished!
The two major features of this update would be an updated world generation
algorithm and survival mode. In this post, I'll give a brief overview of these
two features.

## Upgraded World Generation

I have already made a [post](https://jli69.github.io/blog/2025/05/04/voxelworld-biomes.html)
about this but I'll give a short summary here. The new world generation has new
biomes such as deserts and snowy biomes and terrain features such as mountains.
The terrain heightmap generation has also been changed to provide more variation.

The old world generation that was used in `v0.3.0` can still be accessed by changing
the world generation type to be "Old" in the world creation screen.

## Survival Mode

Arguably, survival mode was the bigger and more central feature added in this
update. Prior to `v0.4.0`, Voxelworld only had "creative" mode and therefore 
when implementing survival mode, I had to add a bunch of new features and write 
quite a bit of new code. This took up the bulk of the time developing `v0.4.0`.

Here's a quick overview of some of the new survival features/mechanics:

### Health/Stamina

In survival mode you have to manage your health and stamina. Stamina is used
when the player sprints and can regenerate naturally and slowly over time but
the player can more quickly replenish stamina by eating. If the player runs
out of stamina the player can no longer sprint. As for health, if the player 
runs out of health then the player dies (obviously). Health does not naturally
regenerate and the player can only regenerate health by eating food (so the
system is somewhat similar to Minecraft alpha's health system).

### Inventory/Crafting

The player has an inventory with 36 slots (including the hotbar) which they can
open by pressing the E key. The inventory screen also has a 3 x 3 crafting
grid which they can use to make new blocks, tools, and food. This is different
from Minecraft which only gives the player a 2 x 2 crafting grid in the
inventory unless they use a crafting table (instead it's somewhat more similar
to Minetest's default game which gives the player a 3 x 3 grid right off the
bat in the survival inventory). Otherwise, crafting and the inventory are basically 
the same as in Minecraft.

The player can also drop items with the Q key and then if they are near enough
to a dropped item then they can pick it up and add it to their inventory.
Blocks also can drop items when they are broken.

### Tools

As with any survival game, there need to be tools. There are six tiers of
tools:

**wood > stone > iron > gold > diamond > rainbow**

Wood tools are the lowest tier while rainbow tools are the highest tier.
One difference between Minecraft and Voxelworld is that gold tools are actually
better than iron tools (in durability and speed) but are still worse than diamond
tools. However, since gold is the tier below diamond, then you need at least a
gold pickaxe to mine diamond ore and an iron pickaxe will not be sufficient.

There are five types of tools: A pickaxe, shovel, axe, hoe, and sword.

The pickaxe is for mining ores and stone, a shovel is for digging dirt and sand,
an axe is for chopping wood, a hoe is for tilling dirt and breaking plants 
(such as leaves and cacti) and swords are meant as weapons but serve no
function at the moment as there are no enemies in the game.

Tools have durability and will break when that durability hits 0.

### Furnaces/Chests

I've also implemented chests and furnaces. With chests and furnaces, I had
to implement a `TileData` struct to store the block inventory and other data
(such as fuel left for the furnace and progress on refining/smelting an
item in a furnace). This was stored in a hashmap that was included in each
`Chunk` struct (block ids and geometry were still stored in a vector 
but if there needs to be extra data it was stored in this hashmap). Typically,
most chunks would therefore not have many blocks that needed to have extra
data (since I imagine chests and furnaces with items in them should not be that
common of an occurance) so the hashmap for chest and furnace data hopefully
should not be that much of a performance or memory problem for most users.

## Conclusion

So that's about it for this post, I'm quite happy that I have finally finished
work on `v0.4.0`. I do intend on hopefully adding more features to Voxelworld 
in the future such as monsters and animals (potentially in `v0.5.0`?) but that
might be a while since I have university coming up and I'm not quite sure how
much of my time will be taken up by assorted classes and activities. We'll see.

If this sounds interesting, consider checking out the
[itch.io page](https://nullptr-error.itch.io/voxelworld) for the game.

You can browse the source code on [github](https://github.com/JLi69/voxelworld).

That's about it for now, have a nice day!
