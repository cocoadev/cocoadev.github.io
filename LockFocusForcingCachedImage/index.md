---
layout: page
title: LockFocusForcingCachedImage
---



I'm a bit new to Cocoa programming, and I'm having a peculiar problem with [NSImage lockFocus].  It seems that simply calling lockFocus on one of my images (I'm trying to composite another over it) removes the NSBitmapImageRep representation within the NSImage and replaces it with a NSCachedImageRep.  Ultimately what I'm trying to do here is create an NSImage from a PixMap from the GWorld of the sequence grabber, modify the image somewhat, then send the new data to DecompressSequenceFrameS.

*I know this hasn't been touched in a long time, but...what's the problem with an NSCachedImageRep? It's probably the correct response to lockFocus. After all, you can't draw on the NSBitmapImageRep...I think.*

