---
layout: post
title:  "Using Neovim with Godot"
---

Recently I've been working with a friend on making a game with the Godot game
engine and it's been going quite nicely. Hopefully I'll be able to share some
details about it later.

However, I've mostly been doing development using the in-engine text editor for
Godot instead of my favorite text editor - Neovim. This was mostly because
I never really got an external editor to work with my Godot installation so I
never really bothered. However, I did eventually figure out how to get Neovim
to work with Godot (with a decent amount of help from the internet) and this
post mostly serves to document my setup in case I forget how to recreate it or
if anyone else wants to use Neovim with Godot.

## Helpful Sources

Here are some the sources that I found particularly helpful:

[This Reddit post](https://www.reddit.com/r/neovim/comments/13ski66/neovim_configuration_for_godot_4_lsp_as_simple_as/)

[This YouTube video](https://www.youtube.com/watch?v=cLWgjienc_s)

## Setting up Nvim with Godot

I did make some adjustments for my own config so there are some differences between
the two sources I linked above.

In the file `~/.config/nvim/after/ftplugin/gdscript.lua`:

{% highlight lua %}
local port = os.getenv('GDScript_Port') or '6005'
local cmd = vim.lsp.rpc.connect('127.0.0.1', tonumber(port))

vim.lsp.start({
	name = 'Godot',
	cmd = cmd,
	root_dir = vim.fs.dirname(vim.fs.find({ 'project.godot', '.git' }, { upward = true })[1]),
})
{% endhighlight %}

The file is pretty similar to the one in the Reddit post in that it attempts to
connect to the Godot LSP once a `.gd` file is opened but it does not attempt to
start a Neovim server - I moved that to a different file. I found that the setup
in the post had a problem in that if I opened more than one file it would attempt
to start more than one server which would lead to an error. Essentially, the
Neovim server should only be started once per session or else bad things would
happen.

With this, we can set in Godot that we want to use an external editor and then
whenever we open a `.gd` file in nvim and Godot is open, we then get autocomplete.

In the file `~/.config/nvim/lua/godot-setup.lua`:

{% highlight lua %}
-- Code for setting up neovim with a godot project
local pipe = './godothost'

local function server_running()
	local serverlist = vim.fn.serverlist()
	for _, server in ipairs(serverlist) do
		if pipe == server then
			return true
		end
	end
	return false
end

function start_godot_server()
	-- Attempt to find the root directory of the godot project 
	-- (it must contain a project.godot file)
	local root_dir = vim.fs.dirname(vim.fs.find({ 'project.godot' }, { upward = true })[1])
	-- No root directory found, we are not in a project, do not start the server
	if root_dir == nil then
		return
	end
	if server_running() then
		return
	end
	vim.fn.serverstart(pipe)
	print('started server: ' .. pipe)
end

-- Attempt to start Godot server
start_godot_server()
{% endhighlight %}

This file is called in `init.lua`:

{% highlight lua %}
-- Godot setup
require("godot-setup")
{% endhighlight %}

Upon startup, Neovim checks if it is inside a Godot project by looking for a
`project.godot` file and if it is unable to find such a file, it doesn't attempt
to start the server. There's also a check to make sure a server isn't already
running to avoid the errors mentioned earlier in the post but the check isn't
particularly necessary since this function is only called at startup. If none
of the checks fail, then a server is started at `./godothost` for Godot to
remotely send commands to Neovim (for opening up scripts).

That finishes the setup for Neovim. I also installed the
[nvim-treesitter](https://github.com/nvim-treesitter/nvim-treesitter) plugin
to get better syntax highlighting in `.gd` files (and installed the gdscript
highlighting for nvim-treesitter) but I won't go into detail about it here.

Here's a good video by TJ DeVries on treesitter that I followed:

[https://www.youtube.com/watch?v=MpnjYb-t12A](https://www.youtube.com/watch?v=MpnjYb-t12A)

I also recommend installing the [telsecope](https://github.com/nvim-telescope/telescope.nvim)
plugin, it makes navigating projects really nice (not just for Godot).

However, those two plugins technically aren't required.

## The Godot Part

As for configuring Godot, go to `Editor > Editor Settings > Text Editor > External`.

Enable `Use External Editor`.

For Neovim, I set up a quick script (following one of the comments on the Reddit
post but with my own changes):

{% highlight bash %}
#!/bin/sh

# A script that can be called from Godot to open up a new file in Neovim
# For the flatpak version of Godot
flatpak-spawn --host nvim --server ./godothost --remote-send "<esc>:e $1<CR>"
{% endhighlight %}

I put the script in `~/.config/nvim/`.

I installed Godot with flatpak which means that it is containerized and means that
it can't directly run external programs. I then fixed this by prepending the
command with `flatpak-spawn --host` and it worked. This step is unnecessary
if you installed Godot via a package manager that doesn't containerize applications
or simply downloaded the uncontainerized binary from the official Godot website.

The script takes one argument: the file being opened.

Make sure the script is executable and then add the path to the script in
`Exec Path`. I left the `Exec Flags` to be `{file}`, unchanged from the default.

From there, that should be about it. You can now run Neovim as an external editor
from Godot! 

At least if you follow these steps:

1. Open Godot and then opn the project you want to edit
2. Open a terminal and navigate to the root directory of the Godot project you opened
3. Run nvim in that root directory (the directory with `project.godot`)
4. Open a script from the Godot editor - it should then be opened in Neovim.

But yeah, I definitely hope to at least have a little bit more fun in Neovim
editing Godot scripts. Vim motions are just too good.

Anyway, that's it, have a nice day!
