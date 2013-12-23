---
layout: page
title: OpenGlProfiler
---



Hello,

We have a cocoa app that uses a custom sub-class of an openglview,  with an NSTimer calling a drawrect method.  When we run openGL Profiler the FPS always displays 0.0.  With other apps (like the apple gl showpiece) the FPS readout works,  so I don't think we have a broken copy of opengl profiler.

We're in the process of optimizing our code so a FPS benchmark is crucial to knowing how much we're speeding things up.  I would guess that right now the app is running around 60fps on average,  so 0.0 is definitely an incorrect read-out.

Has anyone had a similar problem before,  or have an idea what might be causing this?  I've looked at the opengl profiler docs,  but there isn't much detailed information.

To provide a little more detail about our code,  some of the gl commands occur in the drawrect method of the glview,  and some gl commands occur in a separate class (the "Scene").  

Pseudo code (the drawRect method of the glview):

clear the screen

perform view transformations,  camera stuff,  lighting,  etc

[scene render] ( loop through all the objects and call gl commands)


glFlush


Any insight would be greatly appreciated!

-GW

----
Possibly because you use glFlush, when you should really use self openGLContext] flushBuffer] --[[RbrtPntn

----
Thanks -- looks like that was the problem.  I was also using a openglview that I dragged into the nib,  when what I should have been using was a customview,  with a pixel format set in the init method.  Thanks again.

----
It's utterly trivial to add your own FPS counter, and in fact I would trust that over OpenGL Profiler's counter, partly because of problems like this.

----
Well,  the broken FPS counter in opengl profiler clearly indicated that something in our code needed to be modified.  As trivial as a FPS counter is to implement,  it wouldn't have led to the discovery of the actual root cause of the issue. Not to mention that with opengl profiler I can compare the FPS of my applications to other opengl apps and know that the same measuring stick is being applied. -GW

