---
layout: post
title:  "Generating Trees with Code"
---

![screenshot](/blog/images/trees.jpg)

Over the past few weeks I have been investigating how to generate trees with
code and the results are shown above. I created a simple pine tree and also
created a tree using [Lindenmayer systems](https://en.wikipedia.org/wiki/L-system) 
(or L-Systems).

# The Pine Tree
The pine tree was rather simple, it was just a textured cone as the trunk and
some textured cones as the branches. The code for generating the tree is below:

{% highlight c++ %}
gfx::Vao createPineTreeModel()
{
	gfx::Vao pinetree;
	pinetree.genBuffers(4);
	pinetree.bind();

	glm::mat4 transform;
	glm::mat4 transformtc;

	//Generate trunk
	mesh::Model treemodel = mesh::createConeModel1(12);
	transform = glm::scale(glm::mat4(1.0f), glm::vec3(0.3f, 5.0f, 0.3f));
	mesh::transformModel(treemodel, transform);
	transformtc = glm::scale(glm::mat4(1.0f), glm::vec3(0.5f, 8.0f, 1.0f));
	mesh::transformModelTc(treemodel, transformtc);

	//Generate rest of the pine tree
	glm::vec3 top = glm::vec3(0.0f, 5.0f, 0.0f);
	transformtc = glm::mat4(1.0f);
	transformtc = glm::translate(transformtc, glm::vec3(0.51f, 0.02f, 0.0f));
	transformtc = glm::scale(transformtc, glm::vec3(0.48f, 0.96f, 1.0f));
	for(int i = 0; i < 4; i++) {
		mesh::Model part = mesh::createConeModel2(12);

		float scale = 0.75f + 0.25f * std::pow(1.5f, float(i));
		float height = 1.6f + 0.2f * float(i);
		transform = glm::mat4(1.0f);
		transform = glm::translate(transform, top - glm::vec3(0.0f, height, 0.0f));
		transform = glm::scale(transform, glm::vec3(scale, height, scale));
		float rotation = float(M_PI) / 16.0f * float(i);
		transform = glm::rotate(transform, rotation, glm::vec3(0.0f, 1.0f, 0.0f));
		mesh::transformModel(part, transform);	
		mesh::transformModelTc(part, transformtc);
		
		treemodel = mesh::mergeModels(treemodel, part);
		top.y -= scale * 0.6f;
	}
	pinetree.vertcount = treemodel.indices.size();	
	treemodel.dataToBuffers(pinetree.buffers);

	return pinetree;
}
{% endhighlight %}

First, we generate a cone model and then scale it appropriately and also
transform the texture coordinates from the default model. Next, we then
generate 4 cone models that also transformed appropriately to create the
branches and merge the cones together to form the final tree model.

# L-system tree
When investigating how to make something that looked like an deciduous tree, I
stumbled upon Lindenmayer systems which I found to be quite interesting. 
Lindenmayer systems (or L-systems) allow for representation of self similar
patterns such as fractals or in this case, plants (and in my use case, trees).

I initially came across the concept of L-systems from 
[this video](https://www.youtube.com/watch?v=feNVBEPXAcE) by SimonDev. The
basic idea is that you have a string that could represent features of the
final pattern (usually turtle draw commands such as forward, rotate, and
go back to the start of the branch) and then update this string by using a set
of rules where certain characters get replaced by certain strings and with
each iteration you create a more complex yet self similar structure. This string
is generated from a very simple starting string known as the 'axiom'.

A very simple example given from wikipedia is that you start with a string
`A` and replace `A` with `AB` while `B` gets replaced with `A`. Iterating on
this by hand, we are able to create the following sequence:

Iteration 0: `A` (the axiom)

Iteration 1: `AB`

Iteration 2: `ABA`

Iteration 3: `ABAAB`

Iteration 4: `ABAABABA`

Iteration 5: `ABAABABAABAAB`

Each string has the length of a fibonacci number and apparently according to
the wikipedia article on L-systems this was used by Lindenmayer to model algae
growth.

Another example from wikipedia would be the binary tree which can actually
demonstrate how L-systems can generate fractals or self similar structures
in which wikipedia uses `0` and `1` as draw commands `1` represents drawing
a segment and `0` represents drawing a leaf segment. There are also some
other operations for pushing and popping rotation with `[` being push and
`]` being pop. The axiom was `0` and the rules were that `0` became `1[0]0` and
`1` became `11`. Simply doing this by hand for a few iterations gets the following:

Iteration 0: `0`

Iteration 1: `1[0]0`

Iteration 2: `11[1[0]0]1[0]0`

Iteration 3: `1111[11[1[0]0]1[0]0]11[1[0]0]1[0]0`

Here's an image of the 3rd iteration:

![image](https://upload.wikimedia.org/wikipedia/commons/d/da/Graftal3.png)

by 'Svick', licensed under the [CC-BY 3.0 license](https://creativecommons.org/licenses/by/3.0)
from [this wikipedia article](https://en.wikipedia.org/wiki/L-system)

Additionally, this [article](https://ilchoi.weebly.com/procedural-tree-generator.html) 
from Il Choi also helped me understand how to interpret the resulting string
and convert it into a 3D model. I also used the same alphabet he used to represent
a "tree construction string".

The rule that I used to update a string was to convert all `F`s (`F` means grow
forward) into `F[&&>F]F[--F][&&&&-F][&&&&&&&&-F]` where `&` means rotate
about the y axis, `>` means rotate about the x axis in a negative direction, and
`-` means rotate about the z axis in a negative direction.

It took some experimenting but I'm quite happy with how the final result looks.

# "Wind Effect" in Vertex Shader
Plants typically sway and move about in the wind so to simulate that I checked
if the x value for the texture coordinate was greater than 0.5 (as I had it so
that leaves would be in the right half of the texture) the vertex would then
be translated about with some trigonometric functions to create a quite pleasing
wind effect. It's kind of subtle on the pine tree but becomes more visible on
the L-system tree as its branches extend farther out from the center and I
had it so that the wind effect would be more pronounced the farther you got from
the central axis of the model.

Here's the vertex shader:
{% highlight glsl %}
#version 330 core

/*
	A vertex shader for trees, takes in vertex position and then translates
	it around to create a wind animation effect
*/

layout(location = 0) in vec4 pos;
layout(location = 1) in vec2 texcoord;
layout(location = 2) in vec3 norm;

uniform float time;

uniform mat4 persp;
uniform mat4 view;
uniform mat4 transform;

uniform vec3 lightdir;
out float lighting;

out vec3 fragpos;

out vec2 tc;

void main()
{
	vec4 transformed = transform * pos;
	if(texcoord.x > 0.5) {
		float dist = length(pos.xz);
		float value = sin(pos.x) * 132.0 + cos(pos.z) * 931.0;
		transformed.y += sin(time * dist + value) * 0.03 * dist;
		transformed.x += cos(time * dist + value) * 0.03 * dist;
		transformed.z += sin(time * dist + value / 2.0) * 0.04 * dist;
	}
	gl_Position = persp * view * transformed;
	fragpos = (transform * pos).xyz;
	lighting = max(-dot(lightdir, norm), 0.0) * 0.7 + 0.3;
	tc = texcoord;
}
{% endhighlight %}

Overall, a quite pleasing effect in my opinion.

# Conclusion
So that summarizes what I've learned and done when I was attempting to 
automatically create trees with code. I like the results and hopefully I can
then integrate this code into my infinte terrain demo to make the world look
less barren. I'll probably make an update post when I get that done.

Anyway, that's it, have a nice day.
