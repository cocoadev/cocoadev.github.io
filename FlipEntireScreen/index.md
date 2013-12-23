---
layout: page
title: FlipEntireScreen
---

I would like to write or find an application that would flip the entire OS X user interface about the Y-axis so that it appears as if in a mirror and remains usable as usual. 

I know this must be possible because of the negate function (ctrl+option+apple+8); it must be a simple thing in core image to add a transform to the rendering. 

----
Mac OS X's negative screen function is performed by applying a ColorSync/ICC profile to the output device; other ICC based transformations also allow the contrast capabilities and momentary flash effects.

Flipped the screen will involve another solution.

Your solution requires access to a low level section of the display pipeline. You need to intercept the bits after they have been combined and composed. Maybe a graphics card driver hack is the way to go. :-)

-- GrahamMiln
----

There's something similar to that though that OS X does if you have an external display attached. It allows you to rotate the display 90, 180, or 270 degrees. So they must have some interface for performing simple transformations on the output device... just have to find out what it is (can't help you there :P)

-- DanielPeebles
----
That�s a hardware function; it�s only available with certain graphics chips. However, things like FastUserSwitching, DashBoard and FrontRow show that it�s possible, at some level, to use the �screen� content as a texture. The interface used for this is private and subject to change. -JensAyton

