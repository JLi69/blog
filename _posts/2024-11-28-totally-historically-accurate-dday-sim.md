---
layout: post
title: "Totally Historically Accurate D-Day Simulator"
---

![screenshot](/blog/images/thaddaysim1.jpg)

Hello! So for the past week I've been working on a small project called 
'Totally Historically Accurate D-Day Simulator'. The idea behind 
it is that there are two players: one player controls the Axis and the other 
player controls the Allies on D-Day. The Allies have to capture the flag at the 
far left of the map or eliminate all of the Axis Soldiers. The Axis soldiers 
have to defend their flag by building defenses, such as walls and landmines. 
I made the game in Godot and you can check out the source code and read more
about it in the README on [github](https://github.com/JLi69/thaddaysim)
(Note: in the screenshot above the Axis soldiers have 10 hit points while
in the demo that I released on github they have 16 hit points, this was
an update that I have committed to the repo but I have not yet exported the
project into a release as of the writing of this post).

I think I'm mostly done with the features that I want to implement for this
game but I might have to do some balancing/tweaking to the mechanics to ensure
a somewhat fair experience for both sides.

I'll spend the rest of the post explaining the rules of THADDaySim.

## Name

The official name of this game is 'Totally Historically Accurate D-Day Simulator'
and the following abbreviations are okay: 'THADDaySim', 'thaddaysim', 'thaddsim'.

## Rules

### Allies

Like I said in the top of the post, the goal of the Allies is to reach the flag
at the end of the map and by capturing the flag, they liberate France.

![screenshot](/blog/images/thaddaysim2.jpg)

The all important flag.

The allies have the following actions: move, shoot, grenade, and heal.

#### Move

![screenshot](/blog/images/thaddaysim-allied-move.jpg)

Allied soldiers can move anywhere within a 9 x 9 square on their turn so long
as it is not blocked by a wall. 

#### Shoot

![screenshot](/blog/images/thaddaysim-allied-shoot.jpg)

Allied soldiers can shoot in a straight line up to three tiles away. Anyone
hit will be knocked back one tile. Click on a tile and any soldier in that
tile will be shot. Shooting a wall tile that the Axis soldiers placed will damage
it and after two hits will destroy it.

#### Grenade

![screenshot](/blog/images/thaddaysim-allied-grenade.jpg)

Allies soldiers can throw a grenade to any one of four squares and the explosion
will damage any soldiers within a 5 x 5 square. Note that the grenade will not
damage any walls.

#### Heal

![screenshot](/blog/images/thaddaysim-allied-heal.jpg)

Allied soldiers can heal any of their comrades within a two tile straight line
range. Click on a tile and any soldier in that tile will be healed

### Axis

#### Move

![screenshot](/blog/images/thaddaysim-axis-move.jpg)

The axis soldiers can move anywhere in a 13 x 13 square so long as the tile is
not blocked by a wall.

#### Shoot

![screenshot](/blog/images/thaddaysim-axis-shoot.jpg)

The Axis basically shoot in the same way as the Allies except they have a slightly
longer range of 4 tiles rather than 3.

#### Landmine

![screenshot](/blog/images/thaddaysim-axis-landmine.jpg)

Axis soldiers may place landmines on a tile adjacent to them. If someone steps
on a landmine, it explodes and damages all walls and soldiers within a 5 x 5
square.

![screenshot](/blog/images/thaddaysim-axis-landmine2.jpg)

#### Build

![screenshot](/blog/images/thaddaysim-axis-build.jpg)

Axis soldiers can also build fortifications within a 7 x 7 square so long as
their destination square is not blocked by a wall. Allied soldiers either need
to go around these fortifications or shoot them twice to destroy them.

![screenshot](/blog/images/thaddaysim-axis-build2.jpg)

An example of a wall the soldier can build.

![screenshot](/blog/images/thaddaysim-brokenwall.jpg)

One more shot and this wall is done for.

## Conclusion

Anyway, as all of you reading this post can tell, this game is a *perfect*
representation of what happened on D-Day in 1944 and I got all of the historical
facts correct. Anyone who questions me is objectively wrong and probably is a
big dum-dum.

That's it, have a nice day!

Again, check out the game on github: [https://github.com/JLi69/thaddaysim](https://github.com/JLi69/thaddaysim)
