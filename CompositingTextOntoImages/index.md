---
layout: page
title: CompositingTextOntoImages
---

How do you composite text onto images?

----

(Taken from ImageCompositing)
    
NSImage *canvas = [[[NSImage alloc] initWithSize:NSMakeSize(100, 100)] autorelease];
[canvas lockFocus];
NSString *myString = @"Hello World!";
[myString drawAtPoint:NSMakePoint(10, 10) withAttributes:nil];
[canvas unlockFocus];

