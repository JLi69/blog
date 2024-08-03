---
layout: post
title: "\"Flight Simulator\": First Demo!"
---

![screenshot](/blog/images/flightsim.jpg)

![screenshot](/blog/images/flightsim2.jpg)

Over the past few days I have been working on this "flight simulator" program.
I am attempting to build upon the code from my 
[infworld project](https://jli69.github.io/download-pages/infworld.html) - the
terrain only really serves as a pretty background (the only way it can interact
with the plane is when you accidentally crash into it) but I like the way it
looks.

I made the plane model in Blender which is kind of a first for me as in prior
projects I have either only made 2D games but I also want to make 3D games and
projects and so far all of them have only consisted of somewhat uncomplicated
hard coded models (such as cubes and quads) or have auto generated models
(such as the time I tried to 
[generate tree models with code](https://jli69.github.io/blog/2024/06/28/infworld-trees.html))
I learned how to model and use Blender from the YouTuber 
[The Blender Guru](https://www.youtube.com/@blenderguru) and I found his tutorials
to be quite fun and helped me greatly as prior I was kind of scared of using
something like Blender but after getting some practice with it I feel slightly
better and more confident using it for future projects. I intend on adding some
other objects to the game so hopefully I can get even more practice with Blender.

You can find a "gameplay" video [on YouTube](https://www.youtube.com/watch?v=7q6DcBA_SXQ).

## My current thoughts on this project
I have only recently started working on this project over the past week or so
as I was working on [Birb Run](https://nullptr-error.itch.io/birb-run) during most
of the month of July. Overall, this project is still in its early phases so
there likely will be some changes in the future assuming I don't lose motivation
and decide to abandon it.

I'm still not quite confident on whether I think the plane's controls are good
at the moment as I haven't really played any flight simulator games nor have
I attempted to create any 3D flight simulator games so I might have to change
the way the plane flies/is controlled in the future.

I'm also not quite sure how the terrain looks at the moment as to improve
performance, I had to lower the level of detail and while I think the highest
LOD terrain still looks fine, I'm not quite sure on how the lower quality
terrain looks.

Of course, another thing to consider would be potential bugs in the code that I
haven't found and squashed yet. The way the program detects collision between
the plane and terrain is kind of simple but a little questionable and I'm not
confident that it is fully accurate. However, I'm not quite sure on any easy
and performative way I could even detect collision between the terrain and
plane so I'm likely going to keep that code as is. I'm also weirdly paranoid
about there being potential memory leaks in my code but I haven't been able
to locate any at the moment.

## Some interesting things I implemented so far
Overall, when it comes to gameplay there isn't really anything of too much note.
You can fly the plane around and crash into terrain. That's about it. However,
I have implemented some somewhat interesting technical features that I will
briefly discuss here.

# "impfiles"
I realized that it was kind of getting inconvenient for me to hard code all the
shaders/textures/models that I was going to import into the game so I decided to
implement a way where I could just read a file that contained information about
how to import assets into the game and then those shaders/textures/models would
automatically be imported. This led to the creation of "import files" or as I
decided to call them "impfiles". They're quite simple, each impfile is just a
list of entries with properties that are just variable names that correspond with
a string that is the value of that "variable". One limitation is that you can not
nest entries but for my use case I consider this to be an accpetable limitation.
Could I have just used a json/yaml/toml parser library? Probably. But I felt that
using those file formats would have been slightly overkill and I didn't really want
to bother learning the API for those libraries so I decided to instead roll my
own solution in about a day and it works fine. I was even able to implement
comments which is something I believe json doesn't have.

The syntax looks like the following (the file in question is `shaders.impfile`):

{% highlight conf %}
# This is a list of shaders that are to be imported
# Shaders must have the following variables:
# 'vertex' - path to the vertex shader relative to the executable
# 'fragment' - path to the fragment shader relative to the executable

"terrain" {
	"vertex" = "assets/shaders/terrainvert.glsl";
	"fragment" = "assets/shaders/terrainfrag.glsl";
}

"water" {
	"vertex" = "assets/shaders/instancedvert.glsl";
	"fragment" = "assets/shaders/waterfrag.glsl";
}

"skybox" {
	"vertex" = "assets/shaders/skyboxvert.glsl";
	"fragment" = "assets/shaders/skyboxfrag.glsl";
}

"tree" {
	"vertex" = "assets/shaders/tree-vert.glsl";
	"fragment" = "assets/shaders/textured-frag.glsl";
}

"textured" {
	"vertex" = "assets/shaders/vert.glsl";
	"fragment" = "assets/shaders/textured-frag.glsl";
}

"explosion" {
	"vertex" = "assets/shaders/explosionvert.glsl";
	"fragment" = "assets/shaders/explosionfrag.glsl";
}
{% endhighlight %}

# Explosion effect
Of course, when the plane crashes, you would expect a nice big explosion. So I
spent some time researching billboards 
(this [tutorial](https://www.opengl-tutorial.org/intermediate-tutorials/billboards-particles/billboards/)
was very helpful) to create a simple particle system and after spending some
time finding a suitable explosion particle (I finally found one by Kenney:
[https://opengameart.org/content/smoke-particle-assets](https://opengameart.org/content/smoke-particle-assets))
I am quite happy with the effect. The basic idea is that I draw a bunch of
instanced quads that expand outwards and using this vertex shader I was able to
create a nice explosion effect:

{% highlight glsl %}
#version 330 core

layout(location = 0) in vec4 pos;

uniform mat4 persp;
uniform mat4 view;
uniform mat4 transform;
uniform float time;

const float SPEED = 16.0;

out vec2 tc;

void main()
{	
	float id = float(gl_InstanceID);

	vec3 cameraRightWorldSpace = vec3(view[0][0], view[1][0], view[2][0]);
	vec3 cameraUpWorldSpace = vec3(view[0][1], view[1][1], view[2][1]);
	vec4 center = transform * vec4(0.0, 0.0, 0.0, 1.0);
	float maxsz = 16.0 + cos(id) * 6.0;
	float sz = maxsz - maxsz * pow(1.0 - time, 2.0);

	float rotationSpeed = sin(id * cos(id)) * 3.14 / 4.0;
	float rotation = rotationSpeed * time;

	vec2 p = vec2(
		pos.x * cos(rotation) - pos.z * sin(rotation),
		pos.x * sin(rotation) + pos.z * cos(rotation)
	);

	vec3 vertPosWorldSpace =
		center.xyz +
		cameraRightWorldSpace * p.x * sz +
		cameraUpWorldSpace * p.y * sz;

	float angle1 = sin(id * cos(id)) * 2.0 * 3.14;
	float angle2 = cos(sin(id) + cos(id)) * 2.0 * 3.14;
	float d = fract(id * 31.75414) * 10.0 + 10.0;
	vertPosWorldSpace += vec3(
		cos(angle1) * cos(angle2), 
		sin(angle1), 
		cos(angle1) * sin(angle2)
	) * d;
	vertPosWorldSpace += vec3(
		cos(angle1) * cos(angle2) * SPEED, 
		SPEED / 2.0,
		cos(angle1) * sin(angle2) * SPEED
	) * time;

	gl_Position = persp * view * vec4(vertPosWorldSpace.xyz, 1.0);

	tc = pos.xz;
	tc += vec2(1.0, 1.0);
	tc /= 2.0;
}
{% endhighlight %}

And of course, explosions start out hot but then cool off so in the fragment
shader I had it so that as time passed, the explosion particles would go from
orange (the texture was orange already so the initial color multiplier in the
shader was white) to black so that it kind of looked like it went from a fire to
smoke effect:

{% highlight glsl %}

#version 330 core

uniform sampler2D tex;
uniform float time;

in vec2 tc;

out vec4 color;

void main()
{
	color = texture(tex, tc);
	color *= mix(
		vec4(1.0, 1.0, 1.0, 1.0), 
		vec4(0.1, 0.1, 0.1, 1.0),
		min(pow(time / 1.5, 3.0), 1.0)
	);
}
{% endhighlight %}

## My future plans for this "game"
So far I have only really implemented flying and crashing into the ground at the
moment but some other features I want to add eventually are enemy planes that
you have to shoot down and other things in the sky for you to blow up and you
have to destroy as many things as possible before you yourself get shot down. I
think that would be a fun simple arcade style demo game where I can learns some
more things about how to make a 3D game.

Anyway, that's it, have a nice day!
