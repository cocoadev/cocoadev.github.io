---
layout: page
title: CanNSBezierPathBeMulticolored
---

Is possible to have a separate colour for a segment of an NSBezierPath?

Also I am wondering if it is possible to have a separate line width per bezier path segment.

----

It's not possible.  The best alternative is to end the line segment and start a new one with a start point of the previous segment's end point.

For example:
    

[[NSColor blue] set];
[path moveToPoint:<somepoint>];
[path lineToPoint:<somepoint>];
[path stroke];

[[NSColor black] set];
[path lineToPoint:<somepoint>];
[path stroke];



Note - this code is bogus - don't use it. It will draw the path over itself in the last colour set. Colour is not a property of NSBezierpath - you need to use separate NSBezierPath objects.

----

When you call [[NSColor black] set] you are setting a property of the current graphics context. You are NOT setting a property of the bezier path. The bezier path itself hold no context information (like color or clipping region); only properties of the path - line width, control points, etc...

You can set the fill and stoke colors of the context individually on Panther, however. [aColor setFill] and [aColor setStroke] respectively.

-- EliotSimcoe

----

One "way" to do this is to make each drawing object an NSObject subclass that can set its own color, then add it to a display list.

For each object, you can use something like     setNeedsDisplayInRect: given the NSRect of the object to make your display more efficient.

Then you just iterate through the display list at each point in your code where you want to update your graphics context.

Of course, this might be WAAAAAAAY too inefficient for an exceptionally complex drawing, regardless of optimization.
Especially because each NSBezierPath will likely require redrawing a substantial redraw area.
However if your drawing can be done very incrementally, it might work.

----

What you do is an option, but it is still true that NSBezierPath doesn't support multiple colors. It's just a container for a shape.

Depending on what the original poster wanted to do, there may be other and better options than display lists, though. If you find yourself keeping around any drawing object (like an NSBezierPath), you'll often be better off just using an NSImage. Create an NSImage, do your complex drawings in there, and then whenever you need to redraw just paint the NSImage. Dan's idea is best while the bezier path is being edited (if you're doing e.g. a drawing program), while NSImage is a good approach when the path will rarely change, but you may find yourself often redrawing it. For a vector drawing program, you may want a combined approach, where usually the NSImage is used, but when you're in "edit bezier path elements" mode, you'll bypass the NSImage and mess with your display list directly for a while.

The actual drawing in the NSImage would be done in several parts for the lines. If you also want multi-colored fills (like a gradient), you will probably want to build the full path, ignoring the colors, and then clip the context to that path. Then you can just do a rectangular fill of the entire context with your gradient and it'll be clipped to the path's shape. Or you could even just build a path from thesegments that share a color, fill that, then build a new path with the next couple of same-colored segments, fill that etc. There are lots of fancy things you can do that way. Just remember that Quartz is a very advanced drawing engine, and although it is very fast for what it does, drawing is still one of the slowest operations. So, the more you can cache into an NSImage consisting of the already-rendered pixels, the faster it'll usually get. At least if you need to draw the same unchanged thing several times.

-- UliKusterer

----

So, once you have your NSImage, how do you get it in your NSImageView?  I tried [myView setImage:myImage]; and then [myView setNeedsDisplay]; to no avail.  I can composite to the view, but I get wierdness in the background (all black).

