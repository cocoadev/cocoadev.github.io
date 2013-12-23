---
layout: page
title: PlaneCollisionDetection
---



I'm working on an OpenGL first-person project.
The camera is a cylinder, a plane is.. A plane =)

I use simple math (no vectors and shit =P) to calculate the collisions between these two objects.. And it work perfectly.. Only thing is, after I have "bumped" into any plane (standing up as a wall ofcourse) I cannot "release" from it.. I'm stuck at it no matter which way I go at.. Please help me solve this problem!

Thanks!  --AntonKiland

----

Generally don't you crash when you run into an object or is this bumper planes?

Huh?? --AntonKiland

----

Well, what do you do codewise when you "bump" into the plane?

----

Well, when you crash into something as a plane, doesnt it usually disintegrate? Or is this bumper cars except with a plane?

----
wow, you gave absolutely no details to how you are going about this. How on earth can we help you?
- GormanChristian

----

I've seen this problem a lot when doing regular 2D collision detection.  My guess is what happens is you keep reversing the velocity each frame, and there is no way to get away from the wall.  You might want to try testing collisions 1 frame ahead of time, so you stop the movement before it actually hits the wall.  Of course, you are also gonna want to maybe figure out how much you will move before you actually collide, so that you can allow the camera to move that distance so collisions look nicer.... but then again I really have no idea how you are doing it :) - JamesCallender

