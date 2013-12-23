---
layout: page
title: AvoidingVirtualPixels
---

Does anyone know if when you're drawing in cocoa (read: NSBezierPath) you can somehow tell the graphics context not to draw inbetween pixel, but to adjust the coordinate either by automatically adding or subtracting 0.5, or drawing on both pixels with no transparency?

----

if you're drawing on integer boundaries, you can set up an NSAffineTransform to offset your coordinate system by (0.5, 0.5)

