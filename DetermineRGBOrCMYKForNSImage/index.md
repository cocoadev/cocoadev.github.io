---
layout: page
title: DetermineRGBOrCMYKForNSImage
---

Hi.

I want to determine the color space for any NSImage object - [myImage representations] objectAtIndex:0] colorSpaceName] doesn't quite cut it, it shows me [[RGBColorSpace for nearly every image, even if they are CMYK...

I could query it as an MDItem, for some image files there's : kMDItemColorSpace              = "RGB", for example, but it's not available for all image file types... it's there for tiff files, but not for eps files, for example...

Is there a way to check if an image file contains RGB or CMYK data in Cocoa or Carbon?

----

Did you try the other representations in the array?  I seem to recall that the first representation is usually the cached version which will be in the colour space of your device (which, on modern video out devices, is always RGB).  Any luck with the other reps?

--JeffDisher
----
There's only one for that particular file I test it with...

----
That happens when you write software for something you don't understand. EPS and PDF files don't have a designated color space, they can have several (images, etc). So, Jeff, Thanks for your help, but I asked for something that can not be done.
I query it now with an MDItem object, MDItemCopyAttribute, that's an easy way to get DPI and the color space.

----
You may already know this, but beware that MDItem won't work on files that Spotlight hasn't indexed, for example if the user has excluded a directory or turned it off for an entire drive.

----
Thanks for that - I now do it with NSImageRep.
Is there no way of finding out the DPI and color space for an eps file? There must be.

----
From looking at the Apple docs, there's no immediately obvious way to get this data out of an NSImage, and the functions to extract this data from ImageReps (and CGImages) only apply to bitmaps.

It's not surprising that there would be no function to get the DPI of an EPS image, since these files contain resolution-independent vector art (I know they can also contain embedded bitmap images, but the point is they do a lot more than that, too). Just out of interest, are you sure that the information you're getting back from the MDItem calls doesn't just apply to the TIFF previews that EPS files often include?

As for the colorspace information, perhaps you could use ColorSync?

----
For me (in a NSBitmap category) this worked:
	if (self colorSpaceName] isEqualToString:@"[[NSDeviceCMYKColorSpace"])  do with cmyk etc.

