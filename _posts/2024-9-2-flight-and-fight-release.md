---
layout: post
title: "Flight & Fight is now Released!"
---

![screenshot](/blog/images/flight-and-fight-screenshot3.jpg)

![screenshot](/blog/images/flight-and-fight-screenshot4.jpg)

Over the past couple of months I have been working on a flight simulator/plane
fighting game that I have decided to call "Flight & Fight". I've written some
posts about it [here](https://jli69.github.io/blog/2024/08/02/flightsim.html)
and [here](https://jli69.github.io/blog/2024/08/11/flight-and-fight.html). After
much work and time, I believe I have finally implemented every feature that I
wanted to add to the game and that it is ready for the wider world to see!

Ever since the last post I made about Flight & Fight, I've made some improvements
and updates such as adding more enemies such as blimps (a friend told me that
the model for the blimp looks more like a zeppelin rather than a blimp but I
decided that I'm too lazy to bother changing the names in the files and code so
they are being called blimps) and enemy planes that shoot at the player. I've
also made some other upgrades to the user interface of the game such as adding
a high score screen and a settings menu. Additionally, I added audio using the
library [openal-soft](https://github.com/kcat/openal-soft) in order to get 3D
audio to work and I used [dr_wav](https://github.com/mackron/dr_libs/blob/master/dr_wav.h)
to load wav files. Just a note about OpenAL: apparently from what I was able to
get working, it seems that OpenAL doesn't support multi-channel audio files for
simulating 3D sound effects which led to some weird issues with the audio that
I wasn't able to fix until I converted the sound effect files to a one channel
wav file. Overall, it wasn't too painful to add sound.

Overall, I'm quite happy with how this project looks in the end and I found
the gameplay to be simple but enjoyable. If you're interested in downloading
and trying out the game, you can download it on 
[itch.io](https://nullptr-error.itch.io/flight-fight).

I might make some posts in the future explaining some interesting aspects of the
game such as enemy AI. We'll see.

Anyway, that's it, have a nice day!
