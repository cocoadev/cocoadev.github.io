---
layout: page
title: DrawingInsidePaths
---

If I have created a closed loop path with NSBezierPath, how can I limit drawing to inside that loop -- for instance, if I want to make a call to CG and I want to make sure CG only draws inside that space and clips everything else, what do I do?  Help!!
----
NSBezierPath has this method:
    
- (void)addClip

Intersects the current clipping path, stored in the current
graphics context, with the receivers path, and replaces the
current clipping path with the resulting path. The current
winding rule is applied to determine the clipping area of the
receiver. This method does not affect the receivers path.

