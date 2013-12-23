---
layout: page
title: NeedToSetFlagWhenWindowBeingResized
---

I have a drawRect: in a view that I have in a window that takes a few seconds to fully render the rect.  I want a conditional that lets me run a simple white fill when the user is dragging the window to be larger, so that it does not lag while trying to run the full drawRect.  How can I capture the user-is-dragging-window-to-new-size event so I can set my flag?  Thanks.


blakespot

----

This flag already exists; it's an NSView method called     -inLiveResize.  Just check that for your view when you're about to draw. -- Bo

