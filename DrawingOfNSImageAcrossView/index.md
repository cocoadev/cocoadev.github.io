---
layout: page
title: DrawingOfNSImageAcrossView
---

Does anybody know a good way to draw an NSImage so that it repeats accross and down a view, i.e. a background image for a view that is a pattern? I toyed with some while() functions but had problems with overlapping and spacing. Is there a built in method for this that I am just not seeing? I couldn't seem to find anything on the subject here or on other sites. Thanks, --LoganCollins

----

Check out NSMatrixForTiling --zootbobbalu

I would expect the easiest would be to use [NSColor colorWithPatternImage:*your image*].  Example:

    
NSRect rect = [aView frame];
NSImage *img = [NSImage imageNamed:@"NSApplicationIcon"];
[[NSColor colorWithPatternImage:img] set];
[NSBezierPath fillRect:rect];


I think that should work. -G

*Don't forget to lock focus on the view first, or make sure it already is (i.e.     drawRect:) --JediKnil*

I tried the code above, and I had a problem. When I resized the window, the view and the image becomes all choppy and such. Any pointers?

~~ JoshaChapmanDodson

----
Put the sample code above inside the -drawRect: method of a view.  Then the view will draw correctly when the window is resized znd in all the other cases where views are asked by the framewoks to draw themselves.  In almost every case, drawing outside of drawRect: is counter productive.

