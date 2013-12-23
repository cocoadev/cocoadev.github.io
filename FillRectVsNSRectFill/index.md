---
layout: page
title: FillRectVsNSRectFill
---



I'm trying to fill an NSView subclass (actually ScreenSaverView subclass) with [NSColor clearColor], but [NSBezierPath fillRect:] doesn't work the way I'd expect. First I run:
    
self window] setOpaque:0];
[[[[NSColor clearColor] set];
self window] setBackgroundColor:[[[NSColor clearColor]]; // not sure if this is necessary

Then I try to fill the NSView with clear with:
    [NSBezierPath fillRect:[self frame]];
but it doesn't work! But the following line does work:
    NSRectFill([self frame]);
What's going on here?

----

You have to refresh the NSView.
----
Does that mean NSRectFill draws directly onscreen? Will it be copied to the window's offscreen buffer? EnglaBenny
----
    NSRectFill(...); does not overlay onto the background, it simply blasts the pixels in the specified rectangle to whatever NSColor has been set. However, you can use     NSRectFillUsingOperation(...); to (somewhat) mimick the NSBezierPath method.

----

Ok I ran a side-by-side test on     -[NSBezierPath fillRect:] vs.     NSRectFill(). These are two NSViews whose     drawRect: methods call the rectangle-filling routine, both with pure red at 50% alpha.

http://mcleskey.org/temp/comparison.jpg

Now I think I get it...    -[NSBezierPath fillRect:] overlays a rectangle of the current color over the background.

----

Another difference seems to be that NSRectFill uses pixel coordinates, whereas fillRect does not.  I.e. if I fill a box e.g.     @(10, 10) size: 100x100 it will have a fuzzy edge with fillRect (because the coordinate system is offset by half a pixel) which it does not when using NSRectFill.

