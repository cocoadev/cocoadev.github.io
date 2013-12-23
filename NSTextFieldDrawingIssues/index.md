---
layout: page
title: NSTextFieldDrawingIssues
---



I've subclassed NSView and made it have a semi-transparent background, but now whenever my controls get highlighted, they draw a small box around themselves like so:

http://homepage.mac.com/tylers/Development/images/BoxOnTranslucentBG.jpg

Note that the box only shows up after clicking inside the TextView. (The blue on the left side of the background is from an image in the window. The translucent NSView is positioned over it.) Is there an option I forgot to set in IB or do I have to subclass these controls to get them to look right? (I'm hoping the answer is not the latter)

----
It is quite likely that you implemented DrawRect incorrectly in your custom view.

----

Here's my code:

    
- (void)drawRect:(NSRect)rect
{
	[[NSColor colorWithDeviceWhite:1.0 alpha:0.7] set];
	NSRect insetRect = NSInsetRect(rect, 1.0, 1.0);
	[NSBezierPath fillRect:insetRect];
	
	[[NSColor colorWithDeviceRed:0.6 green:0.6 blue:0.6 alpha:1.0] set];
	NSFrameRectWithWidth(rect, 1.0);
}


If I comment out the last 2 lines, the boxes are still there on the controls but they become transparent (and allow the image in the background to draw itself as if there were nothing over it.

EDIT: Ah, that's because the rect is being inset by 1. Any thoughts on how to make the translucent view draw a border without its subviews doing so? If I wrap the above code in an if statement, what do I need to check for?

UPDATE: Instead of adding an if statement, I replaced rect with [self bounds] in the framing part:

    
- (void)drawRect:(NSRect)rect
{
	[[NSColor colorWithDeviceWhite:1.0 alpha:0.7] set];
	[NSBezierPath fillRect:rect];
		
	[[NSColor colorWithDeviceRed:0.6 green:0.6 blue:0.6 alpha:1.0] set];
	NSFrameRectWithWidth([self bounds], 1.0);
}


So now it fills the rect with the translucent background and then draws the whole frame around the view. (Please correct me if I'm wrong). Is there a more efficient way to do this (ie check to see if a part of the view's edge actually needs redrawing)?

----
You could check to see if the rect covers any of the border, but really, don't bother. This is such a tiny micro-optimization in a piece of code which will not make any real difference anyway. Do the right thing and OptimizeLater.

----
I would recommend using     NSRectFillUsingOperation(rect, NSCompositeSourceOver) instead of     NSBezierPath because it avoids the off-by-0.5 nature of the drawing grid.  --K

----
I am 99.99% sure that they have identical semantics when it comes to pixel locations, do you have any proof otherwise? The behavior is determined by CoreGraphics which is the same no matter how you get to it.

----
Go draw a 1-pixel outline on an NSBezierPath, and then draw a 1-pixel frame rect using NSFrameRect.  The bezier path is blurry because it operates on the Quartz pixel boundaries (which are the infinitely-thin lines between the pixels) but the frame rect is precisely 1 pixel wide.  --K

----
It all depends on where you draw them. To quote the documentation for NSFrameRect:

*Since the frame is drawn inside the rectangle, it will be visible even if drawing is clipped to the rectangle.*

*Because this function does not draw directly on the line, but rather inside it, it uses the current fill color (not stroke color) when drawing.*

In other words, NSFrameRect's coordinates are equivalent to insetting the rect by half the line width. In some circumstances this will avoid antialiasing, in others this will cause it to happen, but in all cases they are still affected by how CoreGraphics handles coordinates and pixel boundaries.

