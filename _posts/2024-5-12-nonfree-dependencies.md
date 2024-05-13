---
layout: post
title:  "Why I don't like using nonfree dependencies"
---

This is going to be an opinion piece where I just give my unsolicited
thoughts on this subject, so if you disagree then that's okay.

As anyone who has programmed before understands, it is impossible to
write anything "from scratch" and you must almost always use some form of a
library or dependency that somebody else wrote in order to build upon it and
solve your specific problem in your program. When using these dependencies,
you must abide by their licenses which can stipulate various conditions for
using such code (for example, GPL code can require that all derviations must
be GPL licensed).

Of course, what I just said is common knowledge in the open source community
and the software development world in general so this brings me to my argument
of why I will try to not integrate proprietary libraries in any program that I
write. I have two primary reasons: I do not want to force the users of my
program to run proprietary code when using software that I wrote and I also want
to protect my own freedom as developer when using these dependencies.

#### **Part I: I do not want to force users of my program to use proprietary code**
I believe that all software that I write should be under a free/libre license
because I believe that it is unethical for me to distribute code to others without
them being able to access the source code and understand what the code is doing.
I like keeping knowledge open and having source code available and under a free
license is one way of sharing knowledge. Moreover, it allows users to ensure
that I am not doing anything malicious to their computer (which I will hopefully
never do), which in a way allows for more trust for my programs.

However, using a proprietary dependency restricts that ability to have the full
source code of my application be free and therefore leads to me distributing
nonfree code which I find to be in violation of my morals. Of course, I will not
judge you if you write proprietary software and I will admit that I
use proprietary software myself but I will ensure any code that I write is
released under a libre license.

#### **Part II: I want to protect my freedom as a developer**

As a developer, I want to ensure that others do not have any method of exploiting
me. In a world where tech companies are growing ever more greedy and intent on
putting profits above people, I want to be careful with what I choose to integrate
my source code with, I do not want to be the victim of a "supply chain attack".

This might come off as "tinfoil hat" thinking but there have already been real 
world examples of companies developing tools and dependencies for developers.
[Unity gained infamy and public backlash](https://arstechnica.com/gaming/2023/09/game-developers-unite-against-unitys-new-per-install-pricing-structure/) 
when they proposed a new change to their license requiring developers to have a 
"runtime fee" that would charge developers whenever their game was installed.
While Unity rolled back their changes after extreme backlash, it still made many
lose trust in the company and it left behind a bad taste for everyone involved.
I do not want to subject myself to the stress that those developers that actually
had their livelihoods threatened by the greedy actions of Unity and I hope that
I never have to go through that scenario myself. I can ensure that by using
only dependencies that have a free license as they are typically more community
driven and the developers are less likely to do anything malicious like Unity and
if they did, then a fork can be created by the community to counter such actions.

Additionally, open source dependencies have the advantage that anyone in the
community can contribute or at least fork the project for their own needs which
gives me the freedom to make adjustments if I do not particularly like some
feature so that I can make my product better. Of course, I haven't had much
experience with that and I am currently too inexperienced to actually contribute
at the moment but other smarter people can 
([one such instance](https://breckyunits.com/ckmeans.html)). This means that
if there are bugs, I am not at the mercy of the developer to fix them
(albeit it might take a long time for such fixes to be merged, 
[such as with this bug in glfw](https://github.com/glfw/glfw/pull/1426), but
that's a whole other problem to be discussed later).

Admittedly open source does come with some problems such as projects being
abandoned, lacking resources or contributers, and more. However, despite these
issues, there are still many large projects with big communities that have
exemplified the positives of open source and those are the dependencies that I
try to use.

#### **Conclusion**
Whenever possible, I will try to ensure that I only use dependencies that have
a free license for any personal project that I work on for ethical and practical
reasons. This is why I will likely never use a proprietary game engine like
Unity or Unreal Engine (though [Godot](https://godotengine.org/),
[bevy](https://bevyengine.org/), and [LÖVE](https://love2d.org/) are cool) and I
will also never use a proprietary library and I will try to find alternatives
whenever possible.

However, if I use open source libraries, there is almost a moral imperative
for me to give back to the community so I also release any code that I write 
under liberal licenses for others to inspect and use in their own projects 
(even if said code is sometimes kind of garbage at times...).

Anyway, hopefully this perspective was interesting and maybe you agreed or
disagreed with it and perhaps I'll write some more opinion articles in the
future.

That's it, have a nice day!
