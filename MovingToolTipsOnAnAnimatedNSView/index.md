---
layout: page
title: MovingToolTipsOnAnAnimatedNSView
---

I have an NSView that is being animated (text scrolls across it) and I want to be able to have tooltips follow (ie. work properly) with the moving objects. I cannot find a way to do this, because when I use setToolTip as needed, I cannot get it to re-display the ToolTip with the new text. So what happens is the old tooltip remains until i move the mouse out of the NSView, and re-enter, then the new ToolTip is finally displayed.

So, how can I get the current ToolTip to fade out and re-display with new text?

--MaksimRogov

----

Unless I've missed the mark, I don't believe ToolTips were meant to work in this way. They appear zeroed at the mouse coordinates and generally only automatically redraw if you move along its owner within its "time to live" (before the fade). Since it's the view's contents that are moving and presumably not the mouse, a ToolTip's 'following' behavior won't work the way I think you want it to. I think what you may have to do to get the control you're looking for is roll your own solution. 

Specifically, it might be worlds easier to just use a NSBezierPath in your view's drawRect: Other incredibly wasteful things might include another view (moving it around as needed) or a transparent window (also moving around as needed). I would try the Bezier first, though as I think it may be more efficient.

Anybody have a better idea?

----

That seems to be the only option, though how I wish I wouldn't have to go through all that! :P

--MaksimRogov

I've had good luck, from a performance standpoint, using child windows, even when over OpenGL. As long as you've got a QuartzExteme video card, the overhead is negligible. I recently used child windows to do a HUD with standard Cocoa GUI elements for an OpenGL Mahjong Solitaire game and it worked great. 

--ShamylZakariya

----
You might find the ToolTip code useful. Maybe not but it's there, anyway.

