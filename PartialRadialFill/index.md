---
layout: page
title: PartialRadialFill
---

Hello!

I am drawing a circle and wish to fill in part of it.  For instance, I have a black circle, and want to fill in the top right quarter of the circle in white, and have the fill go along with the arc of the circle.  Much like only drawing a few slices from a pizza pan.  I have looked at the arc drawing functions but cannot wrap my head around how to begin, do you have any suggestions for doing so? Thankyou!

----
Take a look at     -[NSBezierPath addClip].

----

Thankyou.  I have looked into that but have not found much use for it as it only seems to be effective when dealing with quarters of the circle, but still offers no solution for more definite values.

----
If you can draw lines to limit the area you want to fill, then you can add that area to the clip and you're set. addClip is cumulative, so you can clip to *both* the circle *and* another arbitrary shape, and your fill will just hit the intersection of the two.
----
What is the best way to handle drawing a line at the degrees of the circle?  Is there a more straightforward way of creating the slices?

