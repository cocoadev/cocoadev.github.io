---
layout: page
title: NSScreenForAPoint
---

If I have a point in screen coordinate system, how do I get the NSScreen containing that point?
More precise, I need to know what screen the origin of my window is on.
[myWindow screen] isn't enough because it returns the screen where the largest part of the window is which isn't always the same screen as where the origin is on.

I can not imagine that there isn't a method for this.

----

Have you *read* the documentation for NSScreen? It has a -frame method. You can enumerate the screens and ask each for its -frame and see if NSPointInRect() ... 

----
Yes, this is exactly what I implemented a few minutes after I posted my question.
So simple, don't know why I didn't think of it sooner.

----
Sometimes things aren't obvious until we read the docs. ;-)

