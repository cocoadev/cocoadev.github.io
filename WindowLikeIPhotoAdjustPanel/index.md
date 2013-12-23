---
layout: page
title: WindowLikeIPhotoAdjustPanel
---

How to make a window like the Adjust Panel of iPhoto? I've tried "setBackground:", but it changes only the background of the window, it does not change the title bar. In a subclass of General/NSWindow where I can write the code for the title bar and the close button?

----

One approach is to subclass General/NSWindow and override private methods. But this way lies madness (not to mention it can break in future updates of the OS, thereby angering your users).

Another approach is to create a borderless window (this is supported, see General/NSWindow documentation for details) and use the content area to define a fake titlebar. You'd have to use tracking rectangles to track drags in the fake title area (so users can move your window).

----
Thank you very much!

----
Yes, but why to define a tracking rectangle? A General/NSView with mouseDragged: overriden is not sufficient?

----
http://mattgemmell.com/source/ has examples of what you want - 'HUD Window'

--Tom Lynch
