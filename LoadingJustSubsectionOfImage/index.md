---
layout: page
title: LoadingJustSubsectionOfImage
---



Is there a way to load just part, e.g. bottom right quarter, of an image into an NSImage?  My goal is to display several small tiles (~128X128 pixels) taken from large images (~6000X6000).  Currently I load in the entire image, crop it, and display the cropped version.  The problem is that the NSImage initWithContentsOfFile: method takes several seconds to run on big images. Several seconds each for several tiles really adds up. This is frustrating since most of the full image will not be used anyway.  Ideally I'd like some method like initWithContentsOfFile: withRowRange: columnRange:.  For simple image formats, I could imagine carefully extracting the raw data, and creating and inserting a NSBitmapImageRep, but this would be difficult to do for arbitrary image formats. Any suggestions?

Thanks!

----

look at     drawInRect:fromRect:operation:fraction: --zootbobbalu

----
Cocoa is always going to parse and read the entire image even if you only use a small portion. You *might* get lucky and find some other image-reading library which does not do this, but I'm highly doubtful.

Do you have any control over these images before your app loads them? If you can break them into pieces beforehand, then problem solved.

----
I think you're right that I may have to break up the images.  The large images are shared with other people, but I might be able to make copies, and determine which file to load based on which region I need.  Hopefully the regions don't often fall on a corner where 4 pieces come together...ugh.

Regarding drawInRect, I'm currently using drawAtPoint for cropping, but both need a valid NSImage.  I've tried using initByReferencingFile instead of initWithContentsOfFile, but it just seems to push where the long wait occurs. I.e. with drawInRect:
    
NSSize tileSize;
tileSize.height = 128;
tileSize.width = 128;
NSImage* bigImage = [[NSImage alloc] initWithContentsOfFile: imgLoc];  //long pause here
	
NSRect srcRect;
(srcRect.origin).x = 500.0;
(srcRect.origin).y = 500.0;
srcRect.size = tileSize;
	
NSRect dstRect;
dstRect.origin = NSZeroPoint;
dstRect.size = tileSize;
	
NSImage *canvas = [[NSImage alloc] initWithSize:tileSize];
[canvas lockFocus];
[bigImage drawInRect:dstRect fromRect:srcRect operation:NSCompositeCopy fraction:1.0]; //no pause here
[canvas unlockFocus];
[queryView setImage:canvas];

//...

vs.
    

//...
tileSize.width = 128;
NSImage* bigImage = [[NSImage alloc] initByReferencingFile: imgLoc]; //no pause here	
//snip...

[canvas lockFocus];
[bigImage drawInRect:dstRect fromRect:srcRect operation:NSCompositeCopy fraction:1.0];  //but a long pause here
[canvas unlockFocus];
//...


----
You know, since you already know the tile size you could always draw a placeholder graphic, load the image in a background thread, reload the main view when it's ready, and then cache it in case you need it again.

