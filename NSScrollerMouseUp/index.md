---
layout: page
title: NSScrollerMouseUp
---

Hello there again, I've encountered a problem I've been trying to solve all evening.

In a nutshell I am trying to get the mouseUp on a NSScroller (in a NSScrollView) so as I could coptimize drawing, however the problem is it isn't called if mouseDown is passed to the superclass (i've determined, this by posing as NSScroller to get my subclass in the NSScrollView).

Any ideas? Thanks 

----

NSScroller is probably running a small custom event loop to track the mouse during the drag, so normal event handling procedures do not apply.

You can probably do what you're looking for by doing a     [self performSelector:@selector(mouseReleased) withObject:nil afterDelay:0] in     -mouseDown:, as the normal event loop mode won't come back until the mouse is released. This is a useful technique for running code at the end of mouse-tracking code in general, such as for sliders etc.

----

Thanks alot that solved my problem :^).

