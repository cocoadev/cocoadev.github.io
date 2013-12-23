---
layout: page
title: StartingWithGraphics
---

A place to begin. There is also a CocoaDrawing page which references several useful discussions...

Then there is the old standby tutorial at oreillynet: http://www.macdevcenter.com/pub/a/mac/2001/10/19/cocoa.html

and part two http://www.macdevcenter.com/pub/a/mac/2001/11/06/cocoa.html

----

I'm starting with cocoa programming. I built a window with a NSView inside of it and would like to set the color of a pixel, I tried to use the setColor:atX:y but it doesn't work. Can anybody help me with the correct sequence of instructions to do it? 
Thank you.

Danilo

First thing that came to my head:

1. Create an IBOutlet connection to the NSView.
2. Put the code in the view's drawRect: method or put in between [view lockFocus]...[view unlockFocus].  Draw rect is for custom subclasses and "view" is the name of the IBOutlet.
3. Create an NSBezierPath at the coordinates and size that you want.
4. Set a color and fill it.

If you aren't drawing in the subclass's drawRect:, the un/lockfocus methods tell the system to do any drawing in the view.  Here's a code version.

    -(void)awakeFromNib // or -(void)drawRect:(NSRect)frame or any method, really, just there are special rules for drawRect: shown below
{
    [view lockFocus]; // Don't use this if in drawRect:

    NSBezierPath *b = [NSBezierPath bezierPathWithRect:NSMakeRect(25, 50, 5, 10)]; // Block is at x=25, y=50, and is 5x10 pixels big
    [[NSColor blueColor] set]; // or redColor/yellowColor/greenColor/blackColor... Look at documentation for NSColor
    [b fill]; // Or use [b stroke] to draw the outline of the block.

    [view unlockFocus]; // Again, don't use this if in drawRect:
}



----
Argh. Never use     lockFocus unless you really know what you're doing. If you're just starting out, you don't really know what you're doing.----

It works!!! Thank you very much.

----
It may work this time, but it is not a good way to implement drawing.  All NSView drawing should take place within the view's -drawRect: unless there is a really extraordinary reason not to do it that way.  -drawRect: is called by the AppKit for lots of different reasons, and if the drawing is not done there, errors will almost always result!  Consider how scroll views work with copy-on-scroll and what happens when tabs are selected in tab views.  Furthermore, calling -lockFocus yourself bypasses the AppKit's -display logic and will also lead to strange incorrect behavior in some cases.  Consider what the AppKit has to do when a non-opaque view is added as a subview to your view.  Every attempt to display the non-opaque view must draw all ancestor views until an opaque ancestor is found.  If the drawing code is not in -drawRect: the result will be incorrect.

----

Congrats. For more see topics like QuartzShapeDrawing NSBezierPath or get yourself into AffineMess

