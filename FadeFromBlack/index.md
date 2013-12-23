---
layout: page
title: FadeFromBlack
---

Many OpenGL screen savers floating around the internet have a bad habit of starting off with a harsh white screen when the screen saver engine fades up from black. This problem is caused by improperly initializing OpenGL in the screen saver module. Many modules defer initialization until - (void)startAnimation or -(void)animateOneFrame, when initialization, at least basic initialization, should be done at init time. 

I have put together some simple examples of how to write OpenGL screen savers that do not exhibit this problem. The modules themselves merely display a scene from a trivial GLUT app, so I haven't bothered to compile the examples. But you can do that yourself ;-)

There are two specific examples:


*FadeFromBlack1 - illustrates doing OpenGL drawing within an NSOpenGLView subclass. Some people might prefer to do this.
*FadeFromBlack2 - illustrates doing OpenGL drawing within the ScreenSaverView subclass, and has a simple canned NSOpenGLView subclass that can be reused in other projects. I prefer to do this.


Find them both on my Public iDisk (my member name is mtrent). Also find them on the web: http://homepage.mac.com/mtrent/FileSharing.html

-- MikeTrent

