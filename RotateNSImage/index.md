---
layout: page
title: RotateNSImage
---

I can't seem to find any information on how to rotate an NSImage (either in documentation or searching these pages),  Is it not so simple to do?  How would someone, for example, rotate an NSImage 180 degrees?

----
I'll leave the code to you (it's late at night here) but basically:

 
*Create a new NSImage big enough for the rotated image
*Lock focus on the new NSImage
*Rotate the NSGraphicsContext transform
*Draw the original image
*Unlock focus


Now you have the original image rotated.  Note I have not tried this so if it doesn't work, don't blame me!
----
Really??!  That's the most straightforward way to rotate an image 180 degrees?  I'm surprised, if that's the case (although I often find myself surprised!)

----

I'd use an NSAffineTransform.  It seems to do rotations just fine.  More information is available at:
http://developer.apple.com/documentation/Cocoa/Conceptual/CocoaDrawingGuide/index.html

