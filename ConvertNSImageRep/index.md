---
layout: page
title: ConvertNSImageRep
---

How do you convert one NSImageRep type into another NSImageRep type? Specifically, I want to convert a TIFF sourced NSImage into a PDF sourced NSImage. What is the best way to do this?

Thanks for any help!

-- RyanBates

Same problem: I have a NSImage with a NSBitmapRepresentation and I'd like to have a NSPDFRepresentation. Any help ? Thanks !

----

Correct me if I'm wrong, I've not tried this but can you not:


* Create a new NSPDFImageRepresentation of the correct size.
* Create a new NSImage of the same size.
* Add the PDF rep to the image.
* Lock focus on the NSImage
* Draw the NSBitmapImageRep into the image
* Unlock focus on the NSImage?


-- RobbieDuncan

*Locking focus and drawing into an NSImage will typically not modify any of the NSImageRep<nowiki/>s contained within, but rather create a new NSCachedImageRep and do the drawing there. Obviously if an NSCachedImageRep is already present then it won't make a new one.*

