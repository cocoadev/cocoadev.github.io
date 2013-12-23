---
layout: page
title: DrawingDirectlyOnNSBitmapImageRep
---



In my application I am trying to draw thumbnails of different images on another image.

I took the sample code from following link
http://developer.apple.com/documentation/Cocoa/Conceptual/CocoaDrawingGuide/index.html#//apple_ref/doc/uid/TP40003290
Which I have modified later for my requirements.

    
NSRect offscreenRect = NSMakeRect(0.0, 0.0, 1725.0, 1725.0);
NSBitmapImageRep* offscreenRep = nil;

offscreenRep = [[NSBitmapImageRep alloc] initWithBitmapDataPlanes:nil
                        pixelsWide:offscreenRect.size.width
                        pixelsHigh:offscreenRect.size.height
                        bitsPerSample:8
                        samplesPerPixel:4
                        hasAlpha:YES
                        isPlanar:NO
                        colorSpaceName:NSCalibratedRGBColorSpace
                        bitmapFormat:0
                        bytesPerRow:(4 * offscreenRect.size.width)
                        bitsPerPixel:32];

[NSGraphicsContext saveGraphicsState];
[NSGraphicsContext setCurrentContext:[NSGraphicsContext graphicsContextWithBitmapImageRep:offscreenRep]];

// Draw your content...
NSBitmapImageRep* imageRep = [[NSBitmapImageRep alloc] initWithData:[NSData dataWithContentsOfFile:path]]; //Background image for the container bitmap rep

[imageRep drawInRect:NSMakeRect(0, 0, offscreenRep pixelsWide], offscreenRep pixelsHifh])];


[NSGraphicsContext restoreGraphicsState];


Problem: Only a small portion of the imageRep, is drawn on offscreenRep.
I have made sure the reolution of offscreenRep to be greater than imageRep. So
imageRep fits onto it.
Any thing wrong in the above code?

