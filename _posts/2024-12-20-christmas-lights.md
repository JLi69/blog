---
layout: post
title:  "Light up the Christmas Tree - an interesting Holiday Math Puzzle"
---

So every year at my High School on the day before winter break the school's
Student Council holds an assembly in which we get to take part in some fun events 
(announcing the winners of various holiday themed competitions that were held 
throughout December, Holiday Games, etc). Since it's my senior year of high school 
this was the last Holiday Assembly I'll attend and it was pretty great and I 
had a lot of fun.

I won't go into the details of the assembly but one of the Holiday themed games
that they had some people play was "Light up the Christmas Tree" (or
"Light up the Christmas Lights"? I don't exactly recall, but point is it was
something to do with lighting up Christmas lights). The basic premise follows:

Each grade (Freshman, Sophomores, Juniors, Seniors) had 6 cones that represented
"Christmas Lights" and the winner would be the one that activated (knocked down)
all of the lights first.

The way the lights were activated was that a die was rolled and each cone had a
number on it and the number of the die was the light that would be "inverted".
Essentially, if the light was unactivated (up) it would be knocked down, if it
was activated (down) it would be brought back up.

I believe that the game ended up lasting a wee bit too long. The student council
kids had to intervene and eventually the Juniors (class of 2026) won 
(as a Senior, class of 2025, boo). However, when they first laid out the rules 
for the game, being the math nerd I was, I immediately went "What is the expected 
value (average number) of dice rolls that it would take before all the cones were 
knocked down?". Postulating this to my fellow nerd friends, two of them immediately 
whipped out their chromebooks and started programming up a simulation to get an 
approximate answer. I then followed and wrote a short python script.

The following code isn't the exact script that I wrote but a roughly historically
accurate recreation:

{% highlight python %}
def simulate(count):
    iterations = 0
    for i in range(count):
        lights = [ True, True, True, True, True, True ]

        while True:
            done = True
            for l in lights:
                if l:
                    done = False
                    break

            if done:
                break

            index = random.randrange(6)
            lights[index] = not lights[index]
            iterations += 1
    return float(iterations) / float(count)

print(simulate(100000))
{% endhighlight %}

I believe when I initially ran the script I got something around 83. One of
my friends wrote his simulation in Rust and after running it over 100 million
games he got a figure fluctuating between 83.15ish and 83.2ish.

So it's settled, it would have taken ~83 die rolls (on average) to win the game.

Which is kind of long, and probably why the student council people had to at
least speed it up.

So that's it right? The answer is about 83, everybody can go home now.

Or is it?????

# Finding the EXACT value

Okay, being the math nerd I was, I was not content with just having an
approximate value. One of my friends who wanted to be an engineer just said
"I would have just been happy with a simulation run a million times and called
it a day with the approximation" (typical engineer) but that would not do for
me.

One of the main issues was that the game technically does not have a set end
and could go on indefinitely (if you got REALLY unlucky). However, there are
only a set number of states in which the cones/lights could be in: 2^6 = 64
(6 lights with 2 states: on or off).

Additionally, from each state we can get to one of six states from the die roll
with 1/6 probability.

This means that we can write an equation for any state's expected value in the
following form:

__E0 = 1/6(E1 + E2 + E3 + E4 + E5 + E6) + 1__

E0 is the initial state's expected value and E1, E2, ... , E6 are all the 
expected values of the states you can get to from rolling the die (multiplied
by 1/6 since each of them have 1/6 probability of appearing and we therefore
take the weighted (not really weighted in this case since they have the same
probability and therefore is just a normal average) average of them to get the
expected value) and then we add 1 because we have to roll the die once from this
state.

We can repeat writing this equation for every single state (except the one in
which every cone/light is down or activated, since we already win in that state
and therefore always need 0 rolls).

We therefore generate a systems of equations (with 63 equations!). Therefore,
solving the system will give us every single expected value for all possible
states. Note that this systems of equations accounts for being able to go 
'backwards' when rolling a die:

__E1 = 1/6(E0 + ...) + 1__

We can go back to E0 from E1 and therefore have that value in the equation.

Unfortunately, I'm not insane enough to solve 63 equations by hand. So is all
hope lost? No, this is where our friends Linear Algebra and the comptuer come
into play!

Notice we can rewrite each equation as the following:

__-1 = 1/6(E1 + E2 + E3 + E4 + E5 + E6) - E0__

Using linear algebra, we can represent the coefficients of all of the expected
values in each equation with a 63 x 63 matrix (most of the values in it are 0
with a lot of 1/6s and -1s). Call this matrix M.

We then have a vector (e) that represents all of the expected values and let
the vector that has all -1s in it be represented by v.

Therefore, we have the following matrix-vector equation:

__Me = v.__

We just need to invert M and multiply it by v to get our answer:

__(M^-1)(M)e = (M^-1)v__

__e = (M^-1)v.__

Very cool! Except I don't want to invert a 63 x 63 matrix.

Good news though! We live in the 21st century and have these magic devices called
"computers" that can do it for us!

So I wrote a simple python script to generate the coefficients for each equation
and put them in the appropriate place in the matrix (I used numpy as the linear
algebra library to handle all the number crunching). The script follows:

{% highlight python %}
import numpy as np

def calculate():
    vector = [ -6 ] * 63 # Multiplied by 6
    matrix = []
    for i in range(63):
        coefficients = [ 0 ] * 63
        coefficients[i] = -6 # Multiplied by 6
        indices = []
        for j in range(6):
            bit = 1 << j
            index = i
            if index & bit == 0:
                index += bit
            else:
                index -= bit
            indices.append(index)
            if index == 63:
                continue
            coefficients[index] = 1 # Multiplied by 6
        matrix.append(coefficients)

    inverse = np.linalg.inv(matrix)
    product = np.dot(inverse, vector)
    for i in range(len(product)):
        s = ""
        for j in range(6):
            bit = i & (1 << j)
            if bit == 0:
                # Unactivated
                s += "O"
            else:
                # Activated
                s += "X"
        print(s, " : ", product[i])

calculate()
{% endhighlight %}

I multiplied everything by 6 to keep everything as integers to hopefully preserve
some precision.

Anyway, the final result was ... drumroll please ... `83.2000000000001`!

Just kidding, probably not literally 83.2000000000001 since the extra digit was 
probably due to floating point errors and I'm going to assume that it is actually `83.2`.
I'm too lazy to manually check though.

Oh, and another interesting finding I got was that the expected value for the state
in which there is only one cone/light that needs to be activated is 63 dice rolls
which is kind of insane to think about. It doesn't make intuitive sense but the
math works out.

Also another fun fact: the minimum number of rolls you need to win is of course 6 
and I believe the probability for that is `(1/6)^6 * 6!` which is approximately
`0.0154` or about 1.54%. That's not very high but actually not that impossible
if you think about it and actually surprisingly in the realm of possibility.

Anyway, this was a cool problem to think about and while the game is obviously
not perfect, I still am grateful to whoever came up with the idea since it
introduced me to a pretty cool puzzle as an early Holiday gift.

Anyway, that's it, have a nice day and have a Merry Christmas/Happy Holidays!
