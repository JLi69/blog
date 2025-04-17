---
layout: post
title:  "Astrocards - a game I made to help me memorize Italian Vocabulary"
---

![asteroids](/blog/images/asteroids_screenshot.jpg)

A screenshot of an asteroids game.

So over the past couple of weeks I have been working on a small project called
`Astrocards` and I figured it would be interesting to write up my little
adventure in creating it and my thoughts on it.

## Motivation

My main motivation for creating this project was that around Spring Break I
wanted to learn some basic Italian but the main roadblock that I encountered
was that I needed to memorize a decent bit of vocabulary if I wanted to speak
or write. This could be somewhat of a tedious process and I wanted to make
it more engaging. I could sign up for one of the flashcard studying websites
such as Quizlet but I didn't want to create an account for any of those services
so I decided it would be more interesting to write my own study app as a little
programming challenge/project.

The main inspiration for Astrocards was that there used to be a game on Quizlet
called "Asteroids" and the basic idea was that various asteroids would fall down
the screen and they would be labelled with terms/questions from the flashcard
set you were studying and you had to type the answer corresponding with the label
to destroy the asteroid. I thought it was a somewhat fun game and I remember
playing it in middle school to earn as high of a score as possible. Unfortunately,
last time I checked, I think Quizlet deleted the game or something and the other
games aren't quite the same. Therefore, I decided it would be fun to resurrect
the game in my own vision and use it for my own study purposes.

## Astrocards Development

I initially had kind of a rough start to the project. Since I wanted to support
Italian, I needed a game framework that would support being able to type
accents (characters such as áéíóúàèìòù etc.). I also decided that I wanted to
program this app in Rust. I first tried to use [macroqaud](https://macroquad.rs/)
and while it went okay for a bit, I then realized that I did not have an easy
way to implement typing accents in it. I also tried [ggez](https://ggez.rs/)
but that didn't really work out either. Eventually I just settled on peicing
together my own framework using `glfw` (which did have support for typing
accents and other characters) for the window management, OpenGL for the graphics,
and `egui` for the GUI. It wasn't too bad though since I just reused a lot of
code that I have been using for [Voxelworld](https://github.com/JLi69/voxelworld).

After some effort, I was able to implement the asteroids game and I likely spent
a little too much time making the graphics look pretty (implementing a fire trail
for the asteroids and also adding an explosion effect, both of which I really
like by the way). I also added sound effects (which I downloaded from
[opengameart](https://opengameart.org/), in fact, I pretty much downloaded all
of the artwork from [opengameart](https://opengameart.org/) - so a huge thanks
to the artists that make their work available for others to use in such a way).
You can find the credits for the artwork that I used in `assets/credits.txt`
for Astrocards.

Additionally, I would later decide to implement a "learn" mode in which you can
go through the list of flashcards. You start out with multiple choice questions
but it then goes to free response where you have to type your answer in order
to hopefully introudce the terms and then get you to better memorize them later.

## How to Play Astrocards

The idea behind Astrocards is quite simple: you have to protect a planet from
being destroyed by asteroids. You start with five hitpoints and once you lose
all of them, you lose and get a game over screen. You can destroy asteroids by
typing the answer to the question that they are labelled with and get points.
Once you have destroyed enough asteroids you will level up and asteroids will
spawn more frequently and move faster.

![asteroid](/blog/images/asteroid.png)

An asteroid. Type '7' to destroy it.

There are also red asteroids that are more dangerous. If you get them wrong
(type in an answer that does not go with any asteroid on the screen) then
they will cause you to lose a hitpoint. Additionally, if they hit the bottom of
the screen it will result in an instant loss. They are worth double points though.

![red asteroid](/blog/images/red_asteroid.png)

A red asteroid, be careful!

## Creating Your Own Custom Flashcard Set

I also wanted to make sure that you could create your own flashcard sets to
study what you wanted with Astrocards. To create an Astrocards add a file the
the directory `sets/` in the directory that contains the executable and follow
this format:

{% highlight conf %}
# This is a comment
"set_name" {
    "question" = "answer";
}
{% endhighlight %}

An example: 

{% highlight conf %}
"math" {
    # Addition
    "1 + 1" = "2";
    "2 + 3" = "5";
    # Multiplication
    "2 * 2" = "4";
    "3 * 5" = "15";
}
{% endhighlight %}

## Conclusion

Overall, I would say that this was a fun small project to work on and I might
make some small adjustments to it in the future although I do not intend to
make any major updates to it.

If you think this sounds interesting, check out the github page
[here](https://github.com/JLi69/astrocards).

Anyway that's it, have a nice day!

### Screenshots

![main title](/blog/images/main_title_screenshot.jpg)

A screenshot of the main title.

![learn_screenshot](/blog/images/learn_screenshot.jpg)

A screenshot from "learn mode".

![learn_screenshot](/blog/images/game_over_screenshot.jpg)

Game over!
