---
layout: page
title: ScreenSaverSlowingDownOverTime
---

Right now, I've got a nifty screen saver going, it's a nice flag flapping in the wind. I took it from an NSOpenGLView, and used MikeTrent's "FadeFromBlack2" to model it as a screensaver. Right now, it works fine, though I had to add a, and a glFlush(), because it wasn't updating. After it ran for about an hour, it was lagging horrendously, like down to about 5 frames. Any idea why this might be, and how to speed it up? The only code in the DrawRect: method is that which draws the GL_QUADS and then shifts the Z values from each point to the next. (so vertex[3].z = vertex[2].z, in a for loop kind of like that.) the animateOneFrame method has a [self setNeedsDisplay:YES]. Any tips?

� BobInDaShadows

----

There is probably a memory leak somewhere.

----

I don't know a whole lot about fixing memory leaks -  though i've been the CocoaDev page for them. I don't do much in the drawRect other than gl commands. I only have 1 object, the "FlagOpenGLView", the vertices and such are all just structs. It seems to be like a memory leak but on another note, how do you use the performance tools on a ScreenSaver? Do you run them on ScreenSaver engine when your saver is running?

----

Run your ScreenSaver in SaverLab and run the performance tool on that.

----

Alright, I've managed to stop it's slowing down after I moved the contents of the drawRect method to the animateOneFrame method, and a little more finagling. Thanks a ton, guys! Right now, I just have to get rid of these weird wavy lines that only show up in low resolutions of the saver.

-- BobInDaShadows

