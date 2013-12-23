---
layout: page
title: BezierPathForDockIconCountBadge
---

Any hints for how I might draw the fancy badge that apple uses for drag and drop and the mail count with a bezier path rather than rely on bitmap images as resources?

----

Chech out NSBezierPath's constructor method     bezierPathWithOvalInRect:. Then check out NSAttributedString's documentation so you can draw number's with a white foreground color. You can also get the size of the number so you can make the badge large enough to surround the text drawn over the color filled circle. --zootbobbalu

----

That works great for  a simple oval. I was hoping for pointers to drawing the fancier oval with points a la Apple Mail and iTunes programatically.

----

Mail has images in its bundle to do that.

----
Yeah, that was kind of the OP's point. Hence the "with a bezier path rather than rely on bitmap images as resources?"

----
I think this is a case where a more general forum would be best able to help you -- drawing code in general for tracing a 'jagged oval' ...

----

Check out NSBezierPathCategory --zootbobbalu

----

or google for CTBadge :D  jkp

----

CTBadge uses vector graphics, but still hard-coded bitmaps. 

NSBezierPathCategory is just what I was looking for. Thanks zootbobbalu.

----

CTBadge doesn't have hardcoded *Bitmaps*, but does have hardcoded PDFs of the badge masks

----

No prob, I just fixed a bug that was counting the vertical spacing incorrectly so you might want to take a look. --zootbobbalu

