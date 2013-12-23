---
layout: page
title: ThresholdingImages
---




Here's what I need to do: Take an image of any sort, convert it to an 8-bit bitmap representation, then extract each separate pixel value at a time into a 1-bit bitmap (so effectively generating 256 black and white images, some of which will most likely be blank).

The question is what's the best way to do this using General/NSImage and General/NSBitMapImageRep? The first part looks easy - just create an 8-bit General/NSImageBitMapRep with a suitable colourspace and copy the original into it. But what about the thresholding part? I can scan it pixel by pixel using     -getPixel:atX:y: but that approach sounds like it would be slow... Is there a built-in way to do this, like Carbon's colorMatch procs? Generally speaking, is there a fast pixel accessor method available somewhere, or is it just a case of getting down and dirty and loop over the pixel buffer yourself? --General/GrahamCox

Further question after looking into this: The first part (copying to an 8-bit image) is not so obvious either. If I create an 8-bit General/NSBitmapImageRep, how do I specify its colour table? I can specify its colorSpaceName, and I assume that I need General/NSNamedColorSpace. But that only tells it the type of colour space, not which one. I can't see any accessors on this rep or the base class for setting a specific space, only the space's name. Huh?

----

Graham, you'r far more expert than I am in Cocoa and stuff, so just my two cents: if I get you right, you want to render any image grayscale and then produce an array of 256 images such that the bits in, let's say, image[127] has all pixels set to black that originally had a grayscale value of 127?! Can you assume that the original image is RGB? Then I'd in fact create an array with the 256 1-bit imageReps, really just loop over the pixels of the imageRep (access as a byte buffer, not getPixel:atX:y:, so you'r maybe another few percents quicker), calculate the grayscale value for each pixel (commonly something like: grayValue = 0.3*R + 0.59*G + 0.11*B) and then set the respective pixel of the according 1-bit rep (access like 1bitImage[(int)round(grayValue)]). Should be one single expression for each pixel in your original image, so I'd guess it'd not be too slow (depending on what your after).

Obviously, if the image could also be CYMK, you d have to that that into account. And if you really need defined color-mapping (with colorspaces), you'll have to find another method (ever looked into Core Image?)...

--General/MarCocoa

----

Almost right, except I need to work in 8-bit colour, not grayscale. In fact since I need to loop over my pixels only once as you correctly point out, I'm starting with getPixel:atX:y and will General/OptimizeLater if necessary. Having taken a crack at this, it turns out that looping over the pixels is the easy bit - what I thought was the easy bit is turning out to be the hard bit ;-) Which is: getting an 8-bit colour bitmap representation of my original image. (Note: grayscale doesn't work for my purposes here because more than one colour value in the original image can map to the same gray, and I need to preserve the colour value, not the brightness, to a quantization of 8 bits). So my question has become: How do I get an 8-bit colour General/NSBitmapImageRep? I'm using the function     General/NSColorSpaceFromDepth( 8 ), but I'm still unclear what colourspace this really is, and furthermore how to go from a pixel index value to the actual colour (i.e. the colourtable lookup). By the way, if this sounds like a weird thing to want to do, consider that once you have a 1-bit representation of just one pixel value in the image, you can convert it to a vector form using potrace ;-) --GC

----

Ahh, ok, now I see where you are heading (General/GCDrawKit ?)! Hmm, 8-bit color is somewhat unnatural and (only?) used for General/GIFs or so, isnt it?! I agree with you that there is no way obvious in the docs which to take to convert an image from one "mode" to another (I doubt that Colorspace is the correct term for converting to an indexed 8-bit image). But you could maybe convert the rep into a GIF ([myRep representationUsingType:General/NSGIFImageFileType properties:myPropertiesDiect])? Or would you need to specify a colortable right there in the properties dictionary? Anyways, what you can read in the docs is that the properties for a GIF contain a colortable (check General/PackedRGB) which could be of some help to you, if you decipher the General/NSData object of that table- that is, of course only if you dont have to specify it yourself in the first place when converting you imageRep to GIF... Never tried...

Why not post to the cocoa-dev mailing list- I'd guess on there are enough experts for such kinds of questions... (But it'd be cool if you'd put up any possible solution on here, too)

--General/MarCocoa

----

Turns out that basically Cocoa/Quartz does not support downsampling images to 8-bit colour. It supports drawing them, and it supports upsampling, but not going the other way. I guess this is a deliberate design on Apple's part to move away from indexed images as a standard graphics data type - after all, 32-bit colour is much simpler, right? Well, it is, but there are still useful uses for 8-bit. So..... what to do? One possibility is using Carbon, since General/QuickDraw's General/GWorld does support downsampling, etc. However, what I've decided is to stick in the Cocoa realm and implement my own colour quantization methods. After all this is what it comes down to - how to choose the best *n* colours from a possible 16 million that is most optimum for the image in question. Looping over the pixels is a doddle compared to this ;-) However there is plenty of literature on colour quantization, so I'm going with a couple of standard approaches - uniform (which generally looks crap but is very simple), popular (better), and octree (pretty good). So far I have uniform working (with octree compiling and ready to test) so I know that the overall process is working OK. General/OptimizeLater was wise, because other parts of the processing swamp the image pixel iteration time. So for those who would like to downsample General/NSImages to 8 bits with an adaptive palette in Cocoa will find yet another useful bit of code in General/GCDrawKit ;-) --GC

-update, Octree is now working, and works well. For now, this solves the original problem to my satisfaction. Code will be published in due course, though it's a bit big to post here. --GC

----

Great news! I don't really know, what I might need it for (actually, like Apple, I was happy to see most of the old General/GIFs and indexed images go away), but I am looking forward not only to your code, but also to the, definitely super-cool, feature that you intended to use this algorithm for initially!

--General/MarCocoa
