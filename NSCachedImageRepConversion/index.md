---
layout: page
title: NSCachedImageRepConversion
---

In my program I've got code that generates a quicktime movie from snapshots taken live from my simulation's OpenGL output. It is based off apple sample code which takes NSImages and extracts their NSBitmapImageRep to pass pixel data to the hairy underbelly of QuickDraw & QuickTime.

Now, it used to work just fine, when I was saving NSImages for each frame to NSTemporaryDirectory() on the runtime "record" pass and then subsequently reloading them when I was encoding. I don't encode at runtime simply because it would be too slow, so I record first, to /tmp and then encode later.

However, this weekend I decided to optimize and instead of saving out TiffRepresentations ( which is pretty slow, when you consider that I'm trying to do this 25 times per second ): now I write raw gl image buffer data to one huge flat file and, and then subsequently in the encoding pass ( which can be slow ) I read one image at a time from the huge cache file and create NSImage from that data. I do this by reading the number of bytes for each image ( 24 bit, RGB ) from the file and populating a freshly allocated NSBitmapImageRep's pixel data. I then add this rep to an NSImage and pass that on to my QuickTime code.

That much works fine, I know for a fact that the images are being reconstructed correctly.

The trouble is this: OpenGL and Quicktime have different row-ordering, where OpenGL, like Quartz, considers 0,0 to be lower left and Quicktime considers it to be upper left. So, to prevent having my movies upside-down I call:�

    
[image setFlipped: YES];
[image lockFocusOnrepresentation: bitmapRep];


( note, I'm at work and I might not have typed the above correctly )

This is known to work -- in that if I then save that image to disk as a tiff, via      image [[TIFFRepresentation] writeToDisk: @"somefile" atomically: NO]  and look at it with an image viewer it IS correctly displayed.

However, the quicktime code expects the NSImage to have an NSBitmapImageRep and it seems that when I flip the image, the NSBitmapImageRep is converted to an NSCachedImageRep ( I guess that means it's holding an affine transform for the flipping, instead of actually flipping the bitmap )

Suffice it to say, the QuickTime encoding chokes, because it cannot find an NSBitmapImageRep.

So, what I'm wondering is how can I convert that NSCachedImageRep back to an NSBitmapImageRep? I'd rather not have to flip the scanlines of the bitmap data myself ( though I can, and have before, I just don't want to ).

----

A cached image rep means that the image has been drawn into an offscreen window, and subsequent compositing operations come from that window.  That way the system doesn't have to apply the transform, or other manipulations of the image (say resolution or color space don't match the screen).  As for going from a cached image rep to a regular one, I have no idea.  Maybe you can focus on that image rep, find out its window, and then grab the bits from it?  Or maybe you can tell the cached image rep to draw into a new bitmap image rep.   Also, would the vImage framework work for you in flipping your images?  That might be easier than mucking around converting image reps.

----

I haven't tested it at home, but I wrote a test on my machine at work, just now, and I discovered a low-down-dirty way to make it work. I'm positively ashamed at myself, but it works, and it's a one liner. Instead of returning the flipped image,      return [[NSImage alloc] initWithData: [image TIFFRepresentation]]; 

it works....

