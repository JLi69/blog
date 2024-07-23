---
layout: post
title:  "My Criticisms of Terratest"
---

So it's been well over a year since I released Terratest, which is my attempt at 
creating a clone of the game Terraria. Since its been quite a long time since 
I've bothered putting actual effort into this project, I figured that I now have 
a slightly better perspective on some things that I did wrong that I probably didn't 
know when I was working on the project, so this post will detail those gripes and
issues and perhaps in reflecting on everything I did wrong with this project
I can hopefully grow as a programmer/game developer and make better projects
in the future.

# Code quality
Firstly, one of the major issues that I remember with the project is that the
code was of abhorrent quality and towards the end of the project I just stopped
trying to do things the right way and instead opted for more hacky solutions as
I simply just didn't want to deal with maintaining the blob of spaghetti code.
Since I wrote the project in C, I had to implement a lot of things myself from
scratch. However, whether I was inexperienced or simply lazy (give me some
slack, I was 15 when I was working on this) I probably did a lot of things poorly
or in an unideal manner. Basically, I ended up hardcoding a lot of things and
writing a bunch of atrociously long and unreadable functions. Additionally, I
might have overcomplicated certain parts of the code by using data structures
that likely weren't necessary for the project. The sheer poor quality of the
code likely opened a bunch of potential for bugs and also makes the game way too
difficult to actually maintain. I actually now believe that my motivation for
a project is actually somewhat tied to how maintainable the code is for it
as making my life difficult with spaghetti code usually results in me wanting
to rush to the end and further the spiral of writing poor code.

# Bugs
I can't recall too many specific bugs but with the size of the codebase of
Terratest, I wouldn't be too particularly surprised if there were quite a few
that I haven't discovered yet. A specific bug I recall being reported were
that the final boss (yes, this game has a final boss) would bug out at times
and just fly into the sky forever which would soft-lock the player. This of
course is not good but I wasn't able to reproduce it so I think that bug still
remains. Another bug that I found that mostly came from my poor programming of
the game was that you could hit enemies through walls which probably shouldn't
exist. Overall, most of the bugs in this game come from poor/lazy programming.

# Performance
Terratest is not a very optimized game. For instance, the game does a draw call
for every visible block on the screen which is usually not good for performance
(the better way would have been to probably combine all the blocks into a single
mesh that would then allow a large chunk of blocks to be drawn with one draw call).
It should be also noted that the entire world is loaded into memory which leads to 
Terratest being a RAM hog (last time I checked Terratest would consume 300 MB of RAM 
when a world is fully loaded which is kind of insane). Additionally, I believe that
when I started the project I wanted to have the world generate with negative
y coordinates accessible but I didn't ever bother implementing that meaning
that there is just a massive chunk of memory allocated for absolutely nothing
which is pretty stupid in retrospect. Moreover, the save files are not
optimized and are way larger than they should be (the average save file is about
75 MB). Also, when I was getting a couple of friends to try out the game it seemed
that as a save file progressed, the game just got so much more slow for no reason
that I could understand which sucks. Overall, I did a lot of bad things when it
came to making the game run well.

# Other annoying things
The game lacks a bunch of quality of life features. One example would be that
the crafting menu is pretty tedious to use as you have to manually scroll through
every entry to find what you are looking for. Also, there is no way of rearranging
your inventory which makes it fill up with random junk pretty easily. You can also
only create eight worlds which feels kind of like an arbitrary limit now that I
think about it (the only reason for this limitation was that I was too lazy
to implement support for creating an unlimited number of worlds). There probably
a number of other quality of life features that Terratest should have but I didn't
really bother to add.

# Concluding Thoughts
So, what can we learn from this? Well for one, don't cut corners when adding
features, that arguably was the largest cause of all of these problems that I
have with Terratest. Additionally, make sure your code is easily maintainable.

Anyway, I still am happy that I decided to make this project as I was able to
still gain some experience even though I did a lot of things wrong - doing things
wrong is one of the best ways to grow at anything. Terratest also gave me some
things that I could reuse in other projects (some examples being the Chicken
and Slime enemy artwork that I reused in Scale The Tower and the Chicken enemy
also became the current mascot of Birb Games and the main character of
Birb Run).

However, many of the things I now know I did wrong with this project still somewhat
bother me (I suppose that is the case for a lot of my past projects, you can
never really achieve perfection...) - unfortunately I believe that the code for
Terratest has become way too spaghettified and is an unmaintainable mess that I
dread to even touch now. Therefore, I am abandoning Terratest as a project.
However, I'm still open to the idea of rewriting Terratest in a new programming
language that isn't C (like C++ or Rust). I like messing around in C and I think
it's a pretty decent language but unfortunately C is not really something I could
really use for a large project (with how easy it is to shoot yourself in the foot
and how bare bones it is, perhaps this is a skill issue but I really don't think
I should attempt to make anything C for quite some time, at least not until I
get more experience with programming). I won't archive the Terratest repository
for now (not until I at least create a rewrite of the project, whenever that
is). I still have a lot to learn and I want to ensure my next iteration is
signficantly better than Terratest.

Anyway, that's it, have a nice day!
