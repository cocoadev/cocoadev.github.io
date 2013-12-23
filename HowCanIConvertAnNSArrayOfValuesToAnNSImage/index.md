---
layout: page
title: HowCanIConvertAnNSArrayOfValuesToAnNSImage
---



Sorry, I've tried the docs, searching the CocoaDev website and the Cocoa-dev list archives, but haven't found much pertaining to my problem. This must be a really strange request. 

I have an NSArray of values that I want to convert to an NSImage, preferable TIFF format?, for drawing in an NSView. How can I do this? I believe the key is setting up an NSCustomImageRep. But what then?

Example

array value   NSColor

1                 [NSColor redColor]

2                 [NSColor greenColor]

.
.
.
.


I'm able to understand most Cocoa classes enough to begin to use them and then gain understanding, but NSImage beyond simple import and display is eluding me.

Thanks for any help.

----

Hmm.  I'm not quite sure what you mean.  There's no natural meaning of "convert a value to an image".  I suppose for a color, you might want to create an image with some particular dimensions filled solid with that color?  Is that what you're after?

----

I think your description is correct. I'll try to elaborate further.

I have a 2x2 matrix of elevation values stored in an 1-dimensional NSArray where each value represents the elevation within that square area, say 10 x 10 feet. Now I want to make a gray-scale image where higher elevations are light and lower elevations are dark. So I want to make an image where each value is represented by a single pixel (which represents the 10x10 feet area) and I want to make all pixels with a particular elevation value the same shade of gray.

I want to also extend this so that instead of gray-scale, I can assign a particular color to each pixel representing a certain value.

I hope this makes more sense.

----

Try something like this (I recommend that you supplement this with a quick visit to Apple's online docs):


* Create an empty NSImage using     initWithSize:.
* Create an NSBitmapImageRep with the required dimensions, colorspace and so on (the initializer for doing this has one of the longest signatures in Cocoa:     initWithBitmapDataPlanes:pixelsWide:pixelsHigh:bitsPerSample:samplesPerPixel:hasAlpha:isPlanar:colorSpaceName:bytesPerRow:bitsPerPixel:.
* Add the NSBitmapImageRep to the NSImage.
* Get yourself ready to draw your data.
* Call the     lockFocusOnRepresentation: method of your NSImage. This method basically tells Cocoa and CoreGraphics to treat the NSImageRep as the current drawing canvas.
* Draw your data using the AppKit drawing functions (eg. NSRectFill) or CoreGraphics.
* Call the     unlockFocus method of your NSImage, to restore the previous drawing canvas.
* Your NSImage has a method called     TIFFRepresentation, which will give you an NSData object containing TIFF data.


-ToM

----
I found an example of something similar to what I wanted to do here: http://www.macdevcenter.com/pub/a/mac/2002/08/06/cocoa.html . It does exactly what ToM outlined, but includes sample code which I needed to understand the process.

Thanks ToM. You got me along the correct path.

