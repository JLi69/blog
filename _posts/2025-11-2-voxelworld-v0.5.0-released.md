---
layout: post
title:  "Voxelworld v0.5.0 is now released!"
---

![screenshot](/blog/images/voxelworld-v0.5.0/voxelworld-v0.5.0-screenshot.jpg)

Voxelworld `v0.5.0` is now available for download! This update is on the smaller
side but it still has some interesting features in my opinion. I unfortunately
had less time to work on Voxelworld due to other things happening at university
so I'm not really sure when the next update (`v0.6.0`) will even be released but
I do intend on taking a break from working on Voxelworld to focus on other projects
and also to keep up with things happening at college.

However, going into the actual update, here's a short list summarizing all the stuff 
that was added:

 - Aqua and Uranium ore
 - Aqua tools
 - Cotton plants
 - Flower farming and dyes
 - A new skyblock world type

This update primarily focused on adding some new gameplay features rather than
adding new features to the engine. Anyway, I suppose I should give a couple
thoughts that I have for each feature.

## Aqua & Uranium Ore

You can craft normal and red torches in Voxelworld `v0.4.0` but green and blue
torches were unobtainable in survival mode. That was my main motivation behind
adding uranium ore - in order to give the player a way to craft green torches.
(I guess because the torch is radioactive or something?) You can find uranium
deep underground (around the same area you'd find red ore/diamonds) and then
smelt the ore into uranium ingots. You can then combine the uranium ingot with
a stick and piece of coal in the crafting grid to make a green torch.

![uranium torch crafting recipe](/blog/images/voxelworld-v0.5.0/green-torch-crafting-recipe.jpg)

The crafting recipe for a green torch.

You can also use uranium ingots as furnace fuel (because I think nuclear power
is very cool - even if perhaps burning an ingot in a furnace isn't really how
real life nuclear power works I still thought it would be a fun feature to have).
Other than that, uranium doesn't really have many other uses other than as a
somewhat decent fuel and a green light source but perhaps I can come up with
some ideas in a future update.

As for aqua ore, you can find it at the bottom of oceans and you dig it up with
a shovel rather than a pickaxe because it's buried in sand, not stone. I thought
it would be cool to have an ore beneath the ocean since that would give the player
a reason to go underwater. Aqua gems can be combined with sticks and coal similarly
to uranium ingots in order to make blue torches.

You can also combine aqua gems with diamond tools to make aqua tools. While they
have less durability, aqua tools have the special "magical" ability to make
most blocks drop themselves when they are broken with the right tool. For
example, breaking a stone block with an aqua pickaxe will drop stone instead
of cobblestone or breaking a grass block with an aqua shovel will drop the
grass block instead of dirt. It's basically just Silk Touch from Minecraft.

![crafting recipe for an aqua pickaxe](/blog/images/voxelworld-v0.5.0/aqua-pickaxe-crafting-recipe.jpg)

Crafting an aqua pickaxe.

So yeah, those are the two new ores added in `v0.5.0`, they might not have
a million uses but I think they have potential. At least they allow the player
to obtain all the different types of colored torches.

## Cotton

I needed a way for the player to obtain wool without sheep since I haven't added
animals/monsters to the game yet (I do intend to at some point in the future -
probably in `v0.6.0`) so I figured adding the cotton plant would be the best
substitute for now. You can find it in the wild in "warmer" parts of temperate biomes 
(usually somewhat near deserts). You can then craft it down into seeds which you
can plant and then grow into more cotton plants. Crafting four cotton plants
in a 2 x 2 grid yields one wool block. I really liked the texture I drew for
the third stage of the cotton plant's growth (the "flowering" stage with
some pink flowers) so I decided to make it obtainable if you break the cotton
plant at that stage and you can then transplant it to somewhere else. If you
plant it back in farmland, it will eventually grow into the fully grown cotton
but if you place it on grass, it won't grow so you can use it as decoration.

## Flowers & Dyes

Of course, with wool, you probably want to dye it a rainbow of colors. You can
now also craft flowers into dyes (the three types of flowers yield the three
primary colors). You can combine red and blue to make purple dye and combining
red and yellow gets orange dye. You can't combine yellow and blue to get green
as I just kind of copied Minecraft (which I've done quite a bit in this
project...) by having green dye only be obtained by cooking a cactus in a
furnace. I guess this gives a slight bit of extra use to the cactus. However,
flowers now being useful for dyes, I figured it would be nice to give the player
an easy way to mass produce and renew their source of flowers which is why I
added flower farming. You can break a flower with a hoe to get some flower
seeds which you can then plant in farmland to grow into flowers. You can also
craft a single lump of coal into black dye (you can renew coal by smelting a
log in a furnace).

## Skyblock

![skyblock](/blog/images/voxelworld-v0.5.0/skyblock.jpg)

The final major feature that I want to talk about is the skyblock world type.
[Skyblock](https://minecraft.wiki/w/Tutorial:Skyblock) has been a fairly popular 
Minecraft map so I figured it would be fun to add it directly into Voxelworld as 
a world generation type. It was fairly easy to add, only three chunks really had 
anything in them, everything else was just empty space. 
There's of course the famous skyblock spawn island with the
tree and the chest with ice and lava to make a stone generator, the desert
island with a cactus, sugarcane, and a chest with ice and some wheat seeds
(to allow the player to create an infinite water source and a wheat farm).
There's also a third extra island with some flowers on it and a cotton plant to
allow the player to farm wool and dyes. 

Now you can play skyblock in both Minecraft and Voxelworld!

## Conclusion

So yeah, while this update is on the smaller side, I still think it has some
interesting features to play around with. I have other things that I now need
to work on in real life and I also want to work on some other projects that
aren't Minecraft clones so I likely will pause development on Voxelworld for
some time. I do want to eventually release `v0.6.0` at some point in the future.

From the list of features that I added, I would say that `v0.5.0`'s theme
could be considered "the colorful update" since it added different colors of
dye and wool along with the crafting recipes for the green and blue torch.

If this looks interesting, you can check out Voxelworld on 
[itch.io](https://nullptr-error.itch.io/voxelworld) and you can
browse the source code (and check out previous releases) on
[github](https://github.com/JLi69/voxelworld).

Anyway, that's it, have a nice day!

