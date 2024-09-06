---
layout: post
title: "How does the enemy plane AI work in Flight & Fight?"
---

![screenshot](/blog/images/enemyplane.jpg)

A screenshot of blender open with the plane model textured with the enemy plane
texture.

So a few days ago I released [Flight & Fight](https://nullptr-error.itch.io/flight-fight)
(you can download it on itch.io or read the blog post announcing its release
[here](https://jli69.github.io/blog/2024/09/02/flight-and-fight-release.html)).
One of the more interesting problems I had to face when creating this game was
figuring out how to make the enemy plane AI work. In this post I will provide
a summary of the code and explain how it works.

# The General Idea

The basic idea behind what I want the enemy planes to do is that they are supposed
to be able to locate the player and then rotate in a way such that they are facing
and targeting the player so that when they shoot their bullets, said bullets will
have a decent chance of hitting the player. Of course, there are some other aspects
to consider such as if I make the accuracy to high the game might be too hard and
it wouldn't be that fun to play. Additionally, I want the enemy planes to avoid
crashing into the terrain and also avoid crashing into the player.

# Some Math

At first I tried to make the plane rotate in the direction that would result in
it being able to face the player plane the quickest but this ended up being
rather difficult to pull off and rather buggy. But then I realized that there was
actually a way to measure how much the enemy plane was "targetting" the player
plane: the dot product. I could simply take a normalized vector of the difference
between the enemy plane position and the player plane position and calculate
the dot product between the direction the enemy plane is travelling in. The closer
the dot product is to 1.0, the better the enemy plane is targetting the player and
if the dot product is close to -1.0, the enemy plane is facing away from the player.

Therefore, this just becomes a situation where I have to maximize the dot product
of the normalized position difference vector and the enemy plane direction if I
want to target the player plane and if I want the enemy plane to turn away, I can
simply change the goal to be minimizing the dot product. The way I attempted to
minimize/maximize the dot product was to simply change the orientation of the plane
by a small amount and check the dot product of that temporary transform and then
rotate the enemy in the opposite direction and then check the dot products and
choose whichever one is bigger/smaller.

# The Code

You can find `plane.cpp` on 
[github](https://github.com/JLi69/flight-and-fight/blob/master/src/plane.cpp) 
licensed under the GPLv3 license.

I will be simply posting excerpts from the function `Enemy::updatePlane`.

{% highlight c++ %}
//Difference between player position and object position 
glm::vec3 diff = player.transform.position - transform.position;
float dist = glm::length(diff);
glm::vec3 dir = transform.direction();
glm::vec2 
	diffxz = glm::normalize(glm::vec2(diff.x, diff.z)),
	dirxz = glm::normalize(glm::vec2(dir.x, dir.z));
float dotprod = glm::dot(glm::normalize(diff), dir);
{% endhighlight %}

This is the segment of code where I compute the normalized difference vector
and and the distance between the enemy and the player. I also compute the difference
and direction only the xz plane as well.

{% highlight c++ %}
//Shoot
if(values.at("shoottimer") < 0.0f && 
	dotprod > 0.8f && 
	values.at("rotationdirection") > 0.0f &&
	dist < CHUNK_SZ * 12.0f &&
	!player.crashed) {
	SNDSRC->playid("shoot", transform.position, 2.0f);
	values.at("shoottimer") = SHOOT_COOLDOWN;
	bullets.push_back(gobjs::Bullet(transform, speed, glm::vec3(-8.5f, -0.75f, 8.5f)));
	bullets.push_back(gobjs::Bullet(transform, speed, glm::vec3(8.5f, -0.75f, 8.5f)));
}
{% endhighlight %}

The code here is responsible for spawning bullets once the plane is close enough
and has a good enough target on the player (I chose the treshold to be `0.8` for
the dot product). The bullets are given the same orientation as the enemy plane.

{% highlight c++ %}
if(dist < CHUNK_SZ * 2.0f && dotprod > 0.98f && values.at("rotationtimer") < 0.0f
	|| dist < CHUNK_SZ / 2.0f) {
	values.at("rotationdirection") = -3.0f;
	values.at("rotationtimer") = 6.0f;
}
else if(values.at("rotationtimer") < -30.0f && dist < CHUNK_SZ * 2.0f) {
	values.at("rotationdirection") = -3.0f;
	values.at("rotationtimer") = 6.0f;
}
else if(dotprod < -0.98f && values.at("rotationtimer") < 0.0f)
	values.at("rotationdirection") = 1.0f;
else if(dist > CHUNK_SZ * 12.0f && values.at("rotationtimer") < 0.0f)
	values.at("rotationdirection") = 1.0f;
float rotationdirection = values.at("rotationdirection");
{% endhighlight %}

At this point we also need to determine of the enemy plane should turn around/
change direction. If the enemy plane is too close to the player or is close enough
and has a good target lock on the player and likely has made a few shots, the
enemy plane will attempt to turn around and wait a bit before turning around
and once again attempting to fight the player plane. Once the enemy plane is far
enough way or has oriented itself away from the player enough and enough time
has passed, it will turn around and keep up the assault.

{% highlight c++ %}
float rotationx = transform.rotation.x, rotationy = transform.rotation.y;
float dotprod1, dotprod2;	

transform.rotation.y += ROTATION_Y * dt;
glm::vec3 dir1 = transform.direction();
glm::vec2 dir1xz = glm::normalize(glm::vec2(dir1.x, dir1.z));
dotprod1 = glm::dot(dir1xz, diffxz);
transform.rotation.y = rotationy;	
transform.rotation.y -= ROTATION_Y * dt;
glm::vec3 dir2 = transform.direction();
glm::vec2 dir2xz = glm::normalize(glm::vec2(dir2.x, dir2.z));
dotprod2 = glm::dot(dir2xz, diffxz);
transform.rotation.y = rotationy;

if((!(dotprod < 0.0f && rotationdirection < 0.0f && dist > CHUNK_SZ) &&
	!(dotprod > 0.995f && rotationdirection > 0.0f))) {
	if(dotprod1 <= dotprod2) {
		transform.rotation.y -= ROTATION_Y * dt * rotationdirection;
		transform.rotation.z += ROTATION_Z * dt * rotationdirection;
	}
	else if(dotprod1 > dotprod2) {
		transform.rotation.y += ROTATION_Y * dt * rotationdirection;
		transform.rotation.z -= ROTATION_Z * dt * rotationdirection;
	}
}
else {
	if(transform.rotation.z > 0.0f)
		transform.rotation.z -= ROTATION_Z * dt;
	else if(transform.rotation.z < 0.0f)
		transform.rotation.z += ROTATION_Z * dt;	
}
transform.rotation.z = std::max(transform.rotation.z, -MAX_ROTATION_Z);
transform.rotation.z = std::min(transform.rotation.z, MAX_ROTATION_Z);
{% endhighlight %}

We then update the enemy plane's rotation about the y axis which is also
where we take advantage of `dirxz` and `diffxz` and attempt to figure out where
to rotate by testing two directions and seeing which direction yields the smallest
or largest dot product (based on whether we want to head towards the player or
away from the player). The z axis rotation is updated to have the plane tilt
and bank and if the enemy plane is not rotating about the y axis, the z rotation
will attempt to reset to 0.0.

{% highlight c++ %}
transform.rotation.x += ROTATION_X * dt;
dir1 = transform.direction();
dotprod1 = glm::dot(dir1, diff);
transform.rotation.x = rotationx;	
transform.rotation.x -= ROTATION_X * dt;
dir2 = transform.direction();
dotprod2 = glm::dot(dir2, diff);
transform.rotation.x = rotationx;

if(transform.position.y - y <= 80.0f)
	transform.rotation.x -= ROTATION_X * 4.0f * dt;
else if(values.at("rotationdirection") < 0.0f && dist > CHUNK_SZ) {
	transform.rotation.x -= transform.rotation.x * dt;
	if(std::abs(transform.rotation.x) < 0.02f)
		transform.rotation.x = 0.0f;
}
else if(dist <= CHUNK_SZ) {
	if(dotprod1 <= dotprod2)
		transform.rotation.x += ROTATION_X * dt * 2.0f;
	else if(dotprod1 > dotprod2)
		transform.rotation.x -= ROTATION_X * dt * 2.0f;
}
else if(dotprod1 <= dotprod2 && values.at("rotationdirection") > 0.0f && dotprod < 0.995f)
	transform.rotation.x -= ROTATION_X * dt;
else if(dotprod1 > dotprod2 && values.at("rotationdirection") > 0.0f && dotprod < 0.995f)
	transform.rotation.x += ROTATION_X * dt;

transform.rotation.x = std::max(transform.rotation.x, -glm::radians(70.0f));
transform.rotation.x = std::min(transform.rotation.x, glm::radians(70.0f));
{% endhighlight %}

Finally we have the rotation about the x axis. If the plane is dangerously close
to crashing into the terrain (`y` is the terrain height) then we must rotate upwards
(in the negative direction) to avoid crashing into it. If we are flying away
from the plane plane then the plane will attempt to adjust its rotation about
the x axis back to 0.0. Otherwise, if we are too close to the player plane the
enemy plane will attempt to rotate away and otherwise we use similar logic with
the dot product to determine where the enemy plane should rotate towards.

Anyway, that's pretty much it for the enemy plane AI, it's actually not too
complicated in my opinion and took advantage of some interesting mathematics with
dot products. I hope you found this interesting or helpful.

Anyway, that's it, have a nice day!
