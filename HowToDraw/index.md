---
layout: page
title: HowToDraw
---

, 

If you want your drawing to appear in a window, you should do it in a subclass of     NSView. If you don't have a view, you will need to make one. Create:

* A view class that you want to draw for. See HowToCreateAClassInInterfaceBuilder, HowToCreateAClassInXcode.
* An instance of the class you just created. See HowToInstantiateAClassInInterfaceBuilder.


Then, do your drawing in an override of     -[NSView drawRect:]. (For why, see HowToDrawToAViewOutsideOfDrawRect.)
    
- (void)drawRect:(NSRect)needsDisplayInRect
{
	// Your drawing here.
}


Drawing is done in an area limited only by the precision of floating point numbers, but not everything shows up.     -[NSView bounds] returns an     NSRect describing where the edges of the view are in drawing coordinates. For better performance, you can limit your drawing to the     NSRect passed in to     -drawRect:, which defines the area that needs display. This allows part of the view *not* to be redrawn.

For the same reason that you should do your view drawing inside of     -drawRect:, it's important that you *do all of the drawing you want to for the rectangle passed in*. Before     -drawRect: is sent, the areas marked as needing display are wiped clean, so you cannot draw incrementally without having an intermediate context (such as an     NSImage). See DrawingAnimation.

Drawing commands typically rely on the current     NSGraphicsContext to provide them with additional state. For example, to fill a rectangle, you might do the following:
    
[[NSColor redColor] set]; // Sets current drawing color.
NSRectFill(NSMakeRect(10, 10, 2, 20)); // Defines a rectangle and then fills it with the current drawing color.
[[NSColor colorWithCalibratedRed:0.7 green:0.9 blue:0.3 alpha:1.0] set]; // Sets a new color.
[[NSBezierPath bezierPathWithOvalInRect:NSMakeRect(5, 0, 10, 10)] fill]; // Draws a circle in the new color.


Once your view is able to draw, you have to make sure it does so whenever its state changes. You do this by sending     -[NSView setNeedsDisplay:] or     -[NSView setNeedsDisplayInRect:]. It's a good idea to send these in any setters that change what the view draws.
    
- (void)setBackgroundColor:(NSColor *)aColor
{
	if(aColor == backgroundColor) return;
	[backgroundColor release];
	backgroundColor = [aColor retain];
	[self setNeedsDisplay:YES];
}


A ClippingPath defines an area that drawing can be done into. Drawing done outside of this area is clipped (not shown). Clipping paths do not need to be rectangular and are defined with methods like     -[NSBezierPath addClip]. All drawing done in     -drawRect: has a clipping path set up by the system around the areas that need display. It's faster not to draw than to draw and clip, which is why you should pay attention to     -drawRect:'s argument.

Besides     NSView, you can also draw into     NSImages:
    
NSImage *image = [[[NSImage alloc] initWithSize:NSMakeSize(50, 50)] autorelease];
[image lockFocus];
// Your drawing here.
[image unlockFocus];


You can then draw the image itself:
    
[image drawInRect:[self bounds] fromRect:NSZeroRect operation:NSCompositeCopy fraction:1]; // Passing NSZeroRect causes the entire image to draw.


Some drawing tools:

*     NSView: For drawing into a window (and thereby onto the screen).
*     NSString and     NSAttributedString: For drawing text with fonts, colors, and other attributes.
*     NSImage and     NSImageRep: For drawing bitmap or other image data.
*     NSRectFill() and     NSFrameRect(): For drawing rectangles.
*     NSBezierPath: For drawing lines, curves and clipping paths.
*     NSColor: For setting the color other most drawing is done in.
*     NSGraphicsContext: For changing other drawing attributes.
*     NSPoint,     NSSize and     NSRect: For defining where drawing happens.


When drawing images and rectangles, you should be sure to understand     NSCompositingOperation.

[http://www.paintcodeapp.com PaintCode] is a drawing app that generates Objective-C Cocoa drawing code for you instantly.

----
Discussion?

