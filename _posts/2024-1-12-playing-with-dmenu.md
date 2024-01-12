---
layout: post
title:  "Playing with dmenu: the creation dmenu_alias & lsapps"
---

This is a mini-project that I have worked on over the past couple of days to make
my user experience with [dmenu](https://tools.suckless.org/dmenu/) be a little
more to my taste. On my laptop, I use Ubuntu with the 
[awesome](https://awesomewm.org/) tiling window manager and while awesome
provides a run prompt, I personally prefer using dmenu as I like dmenu's
minimalist design and I also like its autocomplete features.

However, dmenu in its default state is quite good as a run prompt but it isn't
perfect. If you install an application locally or with a package manager like
flatpak then it doesn't get displayed by dmenu and it's kind of a pain to
type out the entire path to the executable. In fact, I recently started getting
interested in trying out the [godot game engine](https://godotengine.org/) but
my only options for installing the most recent version were to either download
the executable from the website and have it in my home folder
(I could copy it to some location in my path but that feels somewhat awkward to
do and I wouldn't get automatic updates). I then did some digging online and
found that I could download it via flatpak but in order to run godot I would 
need to run `flatpak run org.godotengine.Godot` which is rather clunky to type
out. Anyway, I'll get into that later. Additionally, dmenu displays every
executable in `$PATH` and a lot of those applications are terminal applications
without a gui which clutters up the options and it's useless to run those 
applications with dmenu.

Overall, I really do think dmenu is a great application but it doesn't meet all
of my tastes and preferences. It is open-source and the codebase is quite simple
but dmenu already has a good amount of functionality that I can work with and
considering that it was designed with minimalism and simplicity in mind, I feel
that directly adding code to it wouldn't follow "suckless" ideals (also I don't
want to risk adding C code to dmenu as that lead to some memory bugs that I 
don't want to deal with). Instead, I decided that I would create a seperate 
program that would take dmenu's output and then check if it could alias that 
string to another string and then output that string in place of dmenu's output.
I deemed this idea more in line with the unix design philosophy and quickly hacked
together a simple python script:

{% highlight python %}
import os
from sys import stdin, stderr
import sys

# Parse a line
def parse_line(line: str):
    # we assume that the line is of the format
    # string1=string2, with no spaces between '=' and the two strings
    # examples: 
    # vi=nvim
    # top=xterm -e top
    tokens = line.split('=')
    # if it is how we expect it to be, return a tuple of the alias name
    # and the value the string will be aliased to
    if len(tokens) == 2:
        return (tokens[0].rstrip('\n'), tokens[1].rstrip('\n'))
    else:
        # Otherwise, return None
        return None

def parse_config(path: str) -> dict:
    aliases = {} 
    try:
        f = open(path, 'r')
    except OSError:
        # Can't open the file, exit program
        print(f'unable to read alias list: {path}', file=stderr)
        sys.exit(1)

    with f:
        lines = f.readlines()
        for line in lines:
            pair = parse_line(line)
            if pair != None:
                aliases[pair[0]] = pair[1]
            else:
                line_stripped = line.rstrip('\n')
                print(f'error parsing line: {line_stripped}', file=stderr)
    return aliases

def main():
    # Get $HOME (assumes we are on a Unix system)
    HOME = os.getenv('HOME')
    # alias list path is '$HOME/.config/dmenu_alias_list'
    path = HOME + '/.config/dmenu_alias_list'
    # parse the config
    aliases = parse_config(path)
    # read stdin and output the appropriate string
    for line in stdin:
        aliased = aliases.get(line.rstrip('\n'))
        if aliased != None:
            # if an alias exists for that string, output that alias
            print(aliased)
        else:
            # otherwise, return the line stripped of any newlines
            print(line.rstrip('\n'))

if __name__ == '__main__':
    main()
{% endhighlight %}

Consider this code to be under [the unlicense](https://unlicense.org/).

Overall, this was simple enough for my purposes and I created the file
`~/.config/dmenu_alias_list` and then added 
`godot=flatpak run org.godotengine.Godot` to the file to allow me to easily
run the godot engine from dmenu.

However, this isn't the end of the story! I later decided that while this script
was simple, it wasn't perfect. I decided to then create a new executable, this
time written in [go](https://go.dev/) (mainly because I kind of wanted to
create something with go for a while now but I didn't have a good project
to use it for until now). After some programming, I was able to rewrite the
python script in go (you can find the source code 
[here](https://github.com/JLi69/dmenu_alias)) and I then also added some extra
functionality such as being able to give the program another file as an
alias list through a command line argument, adding a `-i` flag to
use `dmenu_alias_list` to generate an input for dmenu, and adding escape sequences
so you can add `\=` to represent `=` without having it actually being parsed
as an equals sign. It's simple enough to serve my purposes and is only 193 lines
of go code (including comments and blank lines).

That still isn't the end of me screwing around with dmenu though.

Remember how I mentioned that dmenu displayed ***every*** application in your
`$PATH`? Well I decided that while I was at it, I might as well create a
program that found every desktop application based on the `*.desktop` files
in `$XDG_DATA_DIRS` and then outputs them for dmenu to use. This is how
`lsapps` was born.

It took some bit of research on the internet, but I eventually was able to hack
together a small program that would list every desktop application the user had
installed on their computer and you can find the source code for `lsapps` 
[here](https://github.com/JLi69/lsapps). It's only 302 lines of code counting
comments and blank lines.

I also added functionality where lsapps could print out a `dmenu_alias_list`
that alias the name of the application with the string to be passed to the shell
to execute the program. I made it so that `lsapps` will prefer
the actual executable string as the name of the application
if it is shorter, doesn't contain any equals signs, doesn't contain any
backslashes (to avoid something like `/usr/bin/*` from showing up in dmenu),
and if it doesn't contain `--` in it.

You can then generate a `dmenu_alias_list` for `dmenu_alias` to use by
simply running the following command: `lsapps -g > ~/.config/dmenu_alias_list`.

The `-g` flag stands for **g**enerate `dmenu_alias_list`.

And you can then invoke dmenu with `lsapps | dmenu | dmenu_alias | sh` to run
dmenu with your desktop applications displayed. I've modified `dmenu_path`
and `dmenu_run` from the official distribution of dmenu to work with 
`dmenu_alias` and `lsapps`:

`dmenu_path`:
{% highlight bash %}
#!/bin/sh

cachedir="${XDG_CACHE_HOME:-"$HOME/.cache"}"
cache="$cachedir/dmenu_run"

[ ! -e "$cachedir" ] && mkdir -p "$cachedir"

IFS=:
if stest -dqr -n "$cache" $PATH; then
	lsapps -g > $HOME/.config/dmenu_alias_list
	lsapps -n | sort -u | tee "$cache"
else
	cat "$cache"
fi
{% endhighlight %}

`dmenu_run`:
{% highlight bash %}
#!/bin/sh
dmenu_path | dmenu "$@" | dmenu_alias | ${SHELL:-"/bin/sh"} &
{% endhighlight %}
By the way the script is designed, you can also run shell code in dmenu as 
you could with vanilla dmenu.

Anyway, now I love dmenu even more and it now fits to my preferences a little
bit more.

After doing a search on the internet though, I realized that I could have
just installed [j4-dmenu-desktop](https://github.com/enkore/j4-dmenu-desktop)
which basically seems to do exactly what I was doing in this article...

My personal justification for why I just didn't waste a couple days of my time
is that I learned some stuff about go and some freedesktop standards.

Fine, I admit it, I wasted my time, but at least I had fun wasting my time.
Anyway, I'm going to stick to my implementation though for now because it seems
to work just fine.

That's it, see you all later!
