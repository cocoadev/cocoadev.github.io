---
layout: page
title: PrintingLargeNSBezierPaths
---

I have a very large NSBezierPath that I'm working with.  At the very least, it has 2048 points, plus 4096 control points.  In my custom NSView class it displays perfectly fine.  When I try to call the NSView's print method (which triggers drawRect in exactly the same way), I get no path.

The view contains an outline, some grid lines, labels, and the curve.  All displays on the printout but the curve.

Does anyone know of a maximum size of NSBezierPath that is acceptable to print?

[update] My issue was not caused by a maximum number of points, but rather my lacking a call to setLineWidth.  I presume that the default line width must be zero (or some very small number), which is displayed on-screen, rather than in a printout.

A line width of zero tells the device to use the smallest possible line.  On screen, it's one pixel, on the printer, it's one dot.  At 1200dpi, they can be hard to see :-)

