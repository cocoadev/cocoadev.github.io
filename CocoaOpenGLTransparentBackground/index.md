---
layout: page
title: CocoaOpenGLTransparentBackground
---

Hi,

I wonder if it's possible to make opengl view to be with transparent background? I would like to draw triangle or something like that.

----

First you probably will have to use the -setOpaque: method on the window or view or something. Someone more experienced with transparency can probably help you more with that.

Then you probably want to set OpenGL's clear colour to something like 1, 1, 1, 0.5 (for semi-transparent background) or 1, 1, 1, 0 (for fully transparent). Like so:

    glClearColor(1, 1, 1, 0);

You can set that in your -init method, or wherever you set up GL state. Finally, in -drawRect:, clear the colour buffer. You probably already do this:

    glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);

That clears both the colour and depth buffer.

Now, none of this is tested, but it should help, at least. Let me know.

-- RobRix

Just an addition to what Rob said, but there's one more thing Cocoa needs for transparent OpenGL stuff.  Take a gander:
    
long zeroOpacity = 0;
self openGLContext] setValues:&zeroOpacity forParameter:[[NSOpenGLCPSurfaceOpacity];

This seems to work.  Also, it prolly doesn't hurt to return NO in your NSOpenGLView<nowiki/>'s isOpaque method.  You can even put it on a transparent window and draw OpenGL directly over everything else with transparency.  It's mad!  

-- BrianMoore
----

Thanks, it really works and looks cool!

----

Thanks this was very helpful! And actually, you NEED to override isOpaque to return NO as Brian suggested. Otherwise you might get into troubles if your underlying window is textured... glClear wasn't working without it.

-- TeeJay

