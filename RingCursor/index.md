---
layout: page
title: RingCursor
---




Panther now allows you to make cursors any size you want!! After playing around with creating custom cursors from scratch, I ran into some problems that you should be aware of. The first thing you should know is that the cursor�s hot spot has to coincide with an opaque portion of the image the cursor is initialized with. This means that if you want to place the hot spot at the center of a ring cursor (like the eraser in Photoshop 7), you have to draw a tiny spot in the middle of the cursor to represent the hot spot. Why Apple forces this on you is beyond me, but I�m sure there�s a good reason for implementing this restriction. Here�s a snippet of code for creating a ring cursor from scratch:

    
- (NSCursor *)ringCursorWithDiameter:(float)diameter {
    NSSize imageSize = NSMakeSize(diameter + 20, diameter);
    NSImage *image = [[[NSImage alloc] initWithSize:imageSize] autorelease];
    [self drawRingWithDiameter:diameter point:NSMakePoint(10, 0) image:image];
    NSPoint hotSpot = NSMakePoint(diameter / 2.0f + 10, diameter / 2.0f);
    return (image) ? [[[NSCursor alloc] initWithImage:image
                                                    hotSpot:hotSpot] autorelease] : nil;
                                
}

- (void)drawRingWithDiameter:(float)diameter point:(NSPoint)point image:(NSImage *) image {
    [image lockFocus];
    [[NSColor clearColor] set];
    NSRect frame = NSMakeRect(point.x, point.y, diameter, diameter);
    NSRectFill(frame);
    frame = NSInsetRect(frame, 1, 1);
    NSBezierPath *circle = [NSBezierPath bezierPathWithOvalInRect:frame];
    frame = NSInsetRect(frame, frame.size.width / 2.0 - 2, frame.size.height / 2.0 - 2); 
    NSBezierPath *dot = [NSBezierPath bezierPathWithOvalInRect:frame];
    [[NSColor whiteColor] set];
    [dot fill];
    [circle setLineWidth:2];
    [circle stroke];
    [[NSColor blackColor] set];
    [circle setLineWidth:1];
    frame = NSInsetRect(frame, 0.5f, 0.5f); 
    dot = [NSBezierPath bezierPathWithOvalInRect:frame];
    [circle stroke];
    [dot fill];
    [image unlockFocus];
}



--zootbobbalu

----

How was it not working?  I used your code, took out the two "dot fill" calls, and the cursor worked fine for me.

