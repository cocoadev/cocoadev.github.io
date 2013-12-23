---
layout: page
title: CTGradientAsBackground
---

I've got an NSView that is drawing a CTGradient in its background, and the view has controls on it. I have an NSSearchField and whenever the search field gets focus or I type in it, the background doesn't draw smoothly (see the edges around the search field?):

http://img117.imageshack.us/img117/7452/guicp1.jpg

But when I resize the window, the background draws fine. Anyone know how to fix this?

----

This is probably due to the way the drawing is called in your drawRect: method. Anything like NSBezierPath, the AppKit drawing functions, or in this case CTGradient must be sent [self bounds] for background drawing, not the NSRect given by drawRect:(NSRect)rect. This is because the rectangle sent by drawRect is the current redraw region, which is called whenever subviews receive or depart focus. Changing this to [self bounds] causes the entire view to redraw, fixing the problem. --LoganCollins

For example:

    
- (void)drawRect:(NSRect)rect {
     [someColor set];
     [NSBezierPath fillRect:[self bounds]];
}


----

Such a simple thing fix. Thanks!

----
Similarly,

    
- (void)drawRect:(NSRect)rect {
     [super drawRect:[self bounds]];

     //Custom drawing here, ex:
     [self softWash:[self bounds] withColor:[NSColor selectedControlColor]];
}


Such code would be used when the background is a custom view separate from the views atop it.  This doesn't seem to at all be an uncommon problem, given that you can see it from time to time even in the Spotlight "Show all" window, in the same position as the above shot.  Gradients!

-Stephen

----

Here's a class that will make it easy to add a background anywhere in your project. It's designed to display gradient backgrounds using CTGradient. It can also display colors, patterns, and images, all at any transparency level. It can also round the corners of the background.

[http://www.mere-mortal-software.com/blog/details.php?d=2007-01-16]

http://www.mere-mortal-software.com/blog/blogimages/2007/01/16/gradient.jpg

If you want to use it as a window background, just modify the window's Content View. Here's a tutorial that shows you how to do it:

[http://www.mere-mortal-software.com/blog/details.php?d=2007-01-08]

