---
layout: page
title: AntiAliasingInScreenSaver
---

Hi people... I got a small interrogation about antialiasing in a screen saver. Well, actually, it doesn't seem to work. Here's the line I use :
    
[[NSGraphicsContext currentContext] setShouldAntialias:YES];


I tried to type it into the init of the ScreenSaverView, and also into the animateOneFrame. No result...

-- Trax

I've seen differences in antialiasing whether I do my drawing in animateOneFrame: vs doing a setNeedsDisplay: and doing my drawing in the drawRect: method. (it was awhile ago, so I may have those two mixed up) ++MarkDalrymple

You may have better results if you ask for the current graphics context after the screen saver is installed in its window -- which happens some time after the module is initialized. Maybe override - (void)viewDidMoveToWindow? Just a guess ... -- MikeTrent

