---
layout: page
title: BestWayToDrawToNSViewPixelByPixel
---



Hi,

What is the easiest way to change the color of just one pixel in an NSView? Should I use an NSBezierPath constrained to just one pixel? a Rect of some sort? Or is there something else?

Thanks for any help!

- P

----

draw an NSBitmapImageRep in the view if you want to control a matrix of pixels. But if you only want to draw a single dot, use an NSBezierPath and fill a rect or an oval.

