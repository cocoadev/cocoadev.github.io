---
layout: page
title: NSShadow
---

How do I use NSShadow to draw a shadow around a view? Not around the view's contents, but just the view itself.

----

I assume you mean that you want a shadow OUTSIDE the bounds/frame of the view. The only way to do this is to draw the shadow as part of the PARENT of the desired view, by carefully managing the clipping path so that your view draws in its parent. You can lock focus on your parent view, draw the shadow (a suitable shadow will be obtained by clipping out your actual view, then drawing a solid rectangle using its frame rect), then put the focus back by unlocking it. Drawing the shadow is easy; look up how to manipulate the clipping path.

