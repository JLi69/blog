---
layout: post
title: "More updates to \"Flight Simulator\" - Now called \"Fight & Flight\"!"
---

![screenshot](/blog/images/flight-and-fight-screenshot1.jpg)

![screenshot](/blog/images/flight-and-fight-screenshot2.jpg)

So, I have made some improvements and updates to the flight simulator game that
I talked about in the [previous post](https://jli69.github.io/blog/2024/08/02/flightsim.html).
I started work on "fight mode" - I want to have it so the player can shoot at
other objects in the sky (such as balloons and enemy planes) and increase a score
counter for every object they destroy and they have to keep fighting until they
get destroyed themselves. I think this could be a fun simple game. From the two
screenshots that I have above, I have already implemented the first enemy
(the hot air balloon) and shooting bullets. The hot air balloon doesn't really
do much, I just plan on having it float up and down in one place and the player
can destroy it for some free points. I want to add a few more enemy types so I
will update this blog as I make some more progress. Additionally, another thing
of note that I wanted to mention was that I really like the trail effect for the
bullet - essentially I just draw a bunch of instanced copies of the bullet model
that trail behind the bullet and it ended up turning out nicer than I anticipated.

![screenshot](/blog/images/flight-and-fight-minimap.jpg)

Another thing that I added to fight mode was a minimap to help you locate enemies,
it's quite useful.

I also added some graphical user interface elements such as a pause menu and a
main menu screen - I used [Nuklear](https://github.com/Immediate-Mode-UI/Nuklear)
to build those menus. After experimenting with Nuklear, I've decided that I really
like it, it's pretty simple and you can create some decent looking GUIs without
too much pain. I will probably look into using Nuklear for future projects as
well.

I don't really have much else to mention in this post, the source code for this
project is now on [github](https://github.com/JLi69/flight-and-fight) so you
can check if out if you are interested.

Anyway, that's it, have a nice day!
