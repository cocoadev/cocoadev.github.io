---
layout: page
title: AppleAntiAliasingThreshold
---

If you need to turn off anti-aliasing in XCode (or any app), just do the following:

    defaults write com.apple.Xcode AppleAntiAliasingThreshold 128

Gotta love Google!! --zootbobbalu

----

**Discussion: Problems with anti-aliasing or half-pixel offsets**

I'm having some trouble drawing a grid in an NSView. It draws OK, but I am having trouble making it look good.

    
 NSRect bds = [self bounds];
     
 NSColor blackColor] set];
 int i;
 for(i = 0; i < bds.size.width; i += 15){
     NSPoint start = NSMakePoint(i,bds.size.height);
     NSPoint end = NSMakePoint(i,0);
     NSBezierPath strokeLineFromPoint:start toPoint:end];
 }
     
 for(i = bds.size.height; i > 0; i -= 15){
     NSPoint start = NSMakePoint(bds.size.width, i);
     NSPoint end = NSMakePoint(0, i);
     [NSBezierPath strokeLineFromPoint:start toPoint:end];
 }


The drawing looks fuzzy. Can I make it black by turning off antialiasing or something? I really just want straight black lines. 

----

Try offsetting the points by 0.5 along x and y. That should do it.

    
 [[NSGraphicsContext currentContext] setShouldAntialias:NO]


----

For some reason I can't get a simple black line on my custom view.
It is sort of gray and I need to draw it a few times to get it black.
Same goes for other colors.

     
 - (void)drawRect:(NSRect)aRect
 {
 	NSRect bounds=[self bounds];
 	NSBezierPath *b=[NSBezierPath bezierPath];
 	[b setLineWidth:1];
 	[[NSColor blackColor]set];
 	[b moveToPoint: NSMakePoint(0,10)];
 	[b lineToPoint: NSMakePoint(bounds.size.width,10)];
 	[b stroke];
 }


What am I doing wrong? (os 10.4.2)

----

Indeed, adding 

    [[NSGraphicsContext currentContext] setShouldAntialias:NO];

fixed this!

----

Try moving everything over by half a pixel.  You may be drawing between pixels and getting anti-aliasing.

----

I think that'll only have the right effect on 10.4 though.. on earlier OSes, you did need to move all of your drawing over.  (Drawing at (0.5,0.5) instead of (0,0), for example.)

Indeed, by default, the coordinate system is set so that pixels are at points in between whole number coordinates in untransformed space. You can either draw at half pixel positions to overcome this, or you can just perform an initial transform moving the origin by 0.5 pixels in the X and Y direction before drawing:

     
 - (void)drawRect:(NSRect)aRect
 {
 	NSRect bounds=[self bounds];
 	NSBezierPath *b=[NSBezierPath bezierPath];
 
        CGContextRef ctx = [[NSGraphicsContext currentContext] graphicsPort];
 
        CGContextTranslateCTM(ctx, -0.5, -0.5);
 
 	[b setLineWidth:1];
 	[[NSColor blackColor]set];
 	[b moveToPoint: NSMakePoint(0,10)];
 	[b lineToPoint: NSMakePoint(bounds.size.width,10)];
 	[b stroke];
 
        CGContextTranslateCTM(ctx, 0.5, 0.5);
 }


This will give you the 1 pixel black line you'd like, but it also preserves anti-aliasing for diagonals, text drawing, and the like.

You can also use [[NSAffineTransform to move the coordinate system around.  You should probably save and restore the graphics state rather than moving it 0.5 each direction.  0.5 is exactly representable as a float, but other transforms that may be in effect might not be, so you could get repeated round-off error.

