---
layout: page
title: NSViewRedrawsItself
---

In my subclass of NSView I have overridden the drawRect method to draw a random rectangle. But the NSView subclass (actually a ScreenSaverView) automatically erases its 'contents' before calling drawRect . How can I tell the ScreenSaverView to not to erase its contents before calling drawRect each time.
----
Override     isOpaque to return     YES.

----

I am sorry I didn't explain my problem clearly. You see I am developing a ScreenSaver. Now In the drawRect method of the ScreenSaver I use the NSBezierPath class to draw a rectangle. Now the problem is that whenever I run my screensaver only one rectangle appears; the ScreenSaver erases the content view. I want to make multiple rectangles until screen saver is running, when the ScreenSaver calls the drawRect method the second time it erases rectangle which was drawn before and then draws a new rectangle, but I want the old rectangle to be there and also a new rectangle to be drawn. Please help.

----

I am not familiar with writing a ScreenSaver so this may be wrong but this sounds like you could resolve the problem by not making the drawRect: call super.  I *think* that the drawRect: implementation in NSView actually does the clearing.  I am not totally sure about this and I don't have time to test it but give it a try, it may do what you want.

At least, I seem to remember some interaction with NSView working this way.

--JeffDisher

----

umm... I am not making drawRect to call super.

----

Then it is something external to that method.  I know that most drawRect: work is done in NSView's implementation, in non-screen saver things, and you are supposed to call that super implementation so that you do get correct things like backgrounds.

This tutorial seems to just draw random stuff on top of whatever is there.  Take a look and see if it helps:
http://cocoadevcentral.com/articles/000011.php

--JeffDisher

----

I think the problem is that the screen saver framework works differently than normal views. Normal views try to minimize refresh and thus only the dirty regions will be drawn on each refresh, whereas the screen saver does AFAIK work mostly in OpenGL mode, and thus, there is no problem redrawing the entire "scene" on each frame (and sort of this is required, since drawing is likely to be double buffered by switching display address and not by copying (as is done with normal applications).

Why does the OP needs to "patch" his old drawings? why not just add to the display list on each frame (if you want previous frames to remain)?


Nothing n the screensaver stuff assumes or even favors using OpenGL.  You're welcome to use Quartz if you want.  Try doing your 'adding new drawing to the screen' in animateOneFrame: rather than drawRect:

