---
layout: page
title: CoreFoundationQuartz
---

Right. I made a cute little screensaver in which you see fireworks in action. Now, after a short time, I realized that Quartz called through Cocoa is agonizingly slow. I don't want to plunge into OpenGL, and personnally, I don't think it's necessary because I stick with 2D graphics. But I do want to learn intensive drawing. I tried Apple's documentation on Core Graphics and, not only I'm having a rough time understanding the logic behind the SavedStates and the "stack", and everything else, I can't even draw a single rectangle. The code examples didn't help me at all. Can anyone show me the path so I can go ahead on it ?

-- Trax

----

I do not think calling Quartz directly will provide much of a speedup. It is slow because it does cpu-rendering in main-memory, using floating point coordinates and applying anti-alias to what is being drawn and so on.

There is a developer example showing the speed of texture upload (which is used to transfer the bitmap from main memory to the screen) and with my 133 MHz AGPx2 I think it is around 100 MB/s, so a 1600x1200 bitmap (the resolution I am running) in 32 bit occupies 7.3 MB, which gives an animation speed 13.6 fps -- as a reference, a modern CRT monitor will redraw the screen 80 times pr. second, so we would prefer to get a frame rate of at least 60, to make the animation look smooth.

OpenGL on the other hand will do all the drawing on the actual graphics card, so there is no slow bus to slow it down -- only rendering instructions is sent over the bus, and often things like coordinates can stay on the card for successive frames, plus graphics can be rendered into textures and be reused (moved around, scaled, alpha blended etc. all on the card).

----

This is a problem for many apps, it seems. My example is AquaTerm (coupled with gnuplot) which is astonishly slow, as it seems to draw graphs and especially plots with NSBezierPath s. Compared to the x11 plot viewer for gnuplot, Aqua term is lost. It takes 2 secs for it to update a plot with 4000 points in it - in the x11 viewer I can spin the same 3D-plot in realtime. EnglaBenny

