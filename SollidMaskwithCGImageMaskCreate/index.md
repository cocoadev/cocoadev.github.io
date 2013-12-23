---
layout: page
title: SollidMaskwithCGImageMaskCreate
---

I am porting an OS 9 Application that was developed in C with Code Warrior to OS X with Cocoa. I need to draw NSImages masked so that they are opaque and clipped to their outline. The obvious way to do this is to get a CGImageRef with a method such as this:

    
NSData* cocoaData = [NSBitmapImageRep TIFFRepresentationOfImageRepsInArray: [imageToClip representations]];
CFDataRef carbonData = (CFDataRef)cocoaData;
CGImageSourceRef imageSourceRef = CGImageSourceCreateWithData(carbonData, NULL);
CGImageRef myCGImage = CGImageSourceCreateImageAtIndex(imageSourceRef, 0, NULL);


Then create a mask using CGImageMaskCreate like this:

    
CGImageRef myMask = CGImageMaskCreate(CGImageGetWidth(myCGImage), CGImageGetHeight(myCGImage),
		CGImageGetBitsPerComponent(myCGImage), CGImageGetBitsPerPixel(myCGImage), CGImageGetBytesPerRow(myCGImage),
		CGImageGetDataProvider(myCGImage), NULL, YES);


At this point I can clip the drawing to the mask with code like this:

    
CGContextClipToMask(graphicsContext, *(CGRect*)&drawingRect, myMask);


Now I can draw the image or the mask and to see what the mask is like I can:

    
[[NSColor redColor] set];
CGContextFillRect(graphicsContext, *(CGRect*)&drawingRect);


This won't produce the result I want. The Image is drawn cliped to its outline but the background shows through and the image isn't opaque. The problem is that the mask has the same bit depth as the image. Where the pixels are black it works right but where the pixels are for example gray it creates a transparent mask where only some of the color of the image leaks through. Reducing the bit depth of the mask doesn't help because the darkest pixels of the image will be black and the lighter pixels will be white. What I really need is a solid mask where the white parts are white and pixels with any other value are black. I can't use CGImageCreate to create a solid / white image to mask with the ColorSpace or DecodeArray parameters so I need to figure out how to do it with CGImageMaskCreate, by compositing an image or writing an image filter. Does anyone know how to do this?

----
I fixed up your code blocks, see HowToUseThisSite and TextFormattingRules. I'm afraid I don't know the answer to your question, but at least this way people who do will be able to read your code.

----

I recently had a similar requirement and I think there are a number of ways to crack this particular nut. I will try and find a little more time later to elaborate, but for now consider that you can create a 1-bit mask using CGImageMaskCreate, but actually getting the data into it takes a little bit of cunning. One way is to simply iterate over the image and set the mask pixels accordingly - surprisingly that can be fast. Another way is to use the CGImageCreateWithMaskingColors function which acts like a sort of "chroma key" mask feature - you should be able to set white as the key colour. I haven't used that method myself but looks like it might be what you need. It's 10.4 only though. --GrahamCox

----I've tried creating one bit images and masks but it won't work because it just flatens the image so that for example using an 8 bit grayscale image pixels with values <= 127 will be white and pixels with values >= 128 will be black. The resulting super high contrast black and white image or mask will be useless. The example in the documentation for chromacolor masking appears to be of no use as well because it takes an image with a colored background and removes it. I already have an image surruonded by white like the one shown as a result. What I need to do is create a mask that will let me draw the non-white part of the image over the background without the background showing through. There is an example of how to get a pointer to the pixel data of an image at http://developer.apple.com/qa/qa2007/qa1509.html. Once you have a pointer to the pixels it would be easy to write a small for or while statement to change a pixel to black if not white but how would you then get  the CGBitmap back to an image you could draw in Cocoa or use to create a mask?

----

Not sure if this would help, but you could draw the mask image normally, and then draw the normal image with NSCompositeSourceIn.

----

I still think that CGImageCreateWithMaskingColors will do it. You just need to specify white as the "key" colour. This effectively replaces it with transparent, so whatever you draw it on top of will show through. Isn't that what you want? If not I've obviously not followed. Carefully read http://developer.apple.com/documentation/GraphicsImaging/Conceptual/drawingwithquartz2d/dq_images/chapter_12_section_6.html under the heading "Masking an image with color". It's not 100% clear because in the examples the background is white - it makes more sense in the example where a green background is painted. --GC

----
I tried to do what you suggest using several versions this code:
    
float myMaskingColor[8] = {0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 1.0};
CGImageRef colorMaskedImage = CGImageCreateWithMaskingColors(CGGlyphImage, myMaskingColor);
CGContextDrawImage(graphicsContext, *(CGRect*)&drawingRect, colorMaskedImage);


That last pair of floats that specify the alpha is probably not needed and changing them has no effect. It doesn't work. It messes up the image severely unless the floats are 1.0, 1.0. It seems to let the background come through correctly in the image where there is white but NOT in the white space between the image rect and the outline of the image. This part of the image is painted over the background as white. I messed with a lot of different color values and the white space around the image is always opaque white not transparent.

----

It's hard to say what the problem is because I don't have a test harness to try this in, nor the specific images you're using, but a couple of observations. Maybe your surrounding area is not exactly white, but slightly off, so maybe setting a small tolerance there would help.

That said, the documentation says: * components
An array of color components that specify a color or range of colors to mask the image with. The array must contain 2N values { min[1], max[1], ... min[N], max[N] } where N is the number of components in color space of image. Each value in components must be a **valid image sample value**. If image has integer pixel components, then each value must be in the range [0 .. 2**bitsPerComponent - 1] (where bitsPerComponent is the number of bits/component of image).*

This tells you two things. First, if it's an RGB image then N is 3, so that's why the "alpha" values have no effect. Second, it tells you that colours are not being compared in the 0..1 range of NSColor, but directly in terms of the pixel values (presumably for performance). So if your image is a 24 bit RGB image, where each component is 8 bits, then the components you need will be in the range 0..255. White is R=G=B=255, so try using that as a value, not 1.0. A small tolerance is probably a good idea, say 250..255.

Also, consider that you are creating 4 channels of data from 3. This is not increasing the amount of information in the image, it's only changing it from one form to another. Thus it may not be possible to achieve exactly what you need without supplying further information. QuickDraw for example would supply that extra information in the form of a mask bitmap as a separate input. If you want to get further help, try avoid using vague phrases like "messed up severely", or "I messed with"... what did you actually try? what were the results? Let's see the images. --GC

----
Sorry for my lack of attention to this subject. I've been off riding by bicycle around one of the Rocky Mountain States. I first tried this:
    
CGImageRef colorMaskedImage
= CGImageCreateWithMaskingColors(CGGlyphImage, [NSColor whiteColor]);

This creates an Image just like the source image. Then I tried this:
    
float myMaskingColor[6] = {0xFF, 0xFF, 0xFF, 0xFF, 0xFF, 0xFF};
CGImageRef colorMaskedImage
= CGImageCreateWithMaskingColors(originalImage, myMaskingColor);

This works fairly well. Where there are white parts inside the image the background shows through so the functionality is still not as good as the old QuickDraw function CalcCMask() that actually created a mask that was the same as the outline of an image. To do this I will have to write a function that directly manipulates the pixels of the image but this will be good enough for now. When I write a function equivalent to CalcCMask() I'll post it here. Thanks for your help.

----

CalcCMask (which I'm familiar with, but this is the first time you've mentioned it - if you'd just said you wanted to replicate CalcCMask it would have saved a lot of time) does a different thing from replacing key colours with transparency. It finds the edges of the bitmap based on a seed colour outside of the area to keep (you know this; I summarise it for the benefit of other readers). One way you might tackle this is to use potrace (http://potrace.sourceforge.net/ ), an open source tracer which I've recently been looking at - it does a tremendous job but might be overkill for your needs here. Setting up a potrace bitmap from an NSImage takes a little effort, but it's certainly doable as I just did it ;-)  potrace already works, so it might save you having to write your own code. My code to use potrace with NSImage will be part of GCDrawKit, which I'm almost ready to release - but potrace itself is also required of course, which means you'll have to deal with all the GPL licensing issues that it is bound by. (DrawKit will not have a GPL license, so I'm not sure how that will work out.)

You could also of course just use CalcCMask, given that it simply reads one pixmap and writes another - it would seem to me fairly straightforward to take the offscreen buffer that is wrapped by your NSBitmapImageRep, set up an appropriate PixMap structure to wrap the same buffer and pass it to CalcCMask. Since it is a standalone function (apparently) not reliant on the existence of a fully operational QuickDraw infrastructure, it should still do its job quite happily. While the docs say this function is deprecated, it's always going to be part of Carbon - now only an issue if you plan to use 64bit.

Another approach is an edge tracer of your own devising. potrace already has one of course, but the basic idea behind them is straightforward and has been around forever. I have some code somewhere that does this for QuickDraw BitMap structures - I'll try and unearth it and post it if I can, but it works the same way as potrace in principle and the potrace docs explain how it works pretty well. Yet another way would be to work across each scanline and figure out which areas of background colour belong to the exterior, and which are islands within. Effectively writing your own CalcCMask in other words. Algorithmically I think the edge trace is probably simpler. --GC

----

Your link to potrace.sourceforge.net  404s.

Apple says that QuickDraw is deprecated so I don't want to go through the trouble of writing the code to do this twice - once now with QuickDraw and then again when some future OS stops supporting it. Also the hassle of getting my data from Cocoa to QuickDraw is to be avoided. To me the need to interface Cocoa with the hodgepodge of OSX graphics technologies - QuickDraw, Core Graphics, Core Image and HIView, all of which are dauntingly complex, is a huge weakness in OSX and the Cocoa framework. Also the loss of basic functionality like CalcCMask() is a step backwards.

----

Fixed link (trailing bracket upset it). I agree there are some useful functions in QD that Quartz lacks - all of regions for example. But QuickDraw is never going away, since it's part of Carbon and Carbon is a core technology on top of which Cocoa sits. Even NSWindow sits on top of classic WindowPtrs. So my belief is that it's safe to use. The main fly in the ointment at the moment is Apple's entirely non-technical decision not to expose Carbon 64-bit to the public, even though the engineering is done. So one day when only 64 bit machines exist, this could become an issue. Hopefully they can be persuaded to change their minds about this. Having said that I don't think CalcCMask is "basic" functionality - it's a fairly esoteric function in QD. Walking the edge of the bits a la potrace is an easy solution if you really don't want to link to QD. --GC

----
I imagine you meant "64-bit only" not "only 64 bit", since Apple's lineup has been mostly or all 64-bit for quite some time now, but with full 32-bit compatibility.

----
I read the page to which you linked. Potrace looks amazing but not at all usefull for creating image masks.

----

Why not? I just used it to implement - umm, image masks. OK, you have to write a little bit of code, but it's really trivial. Once you have a vector representation of your bitmap outline (probably without the smoothing/curve fit step in your case), you can use it to set the clipping path of a Quartz context, draw your image and - hey presto! the region outside the edge of the shape has gone. But even if potrace as a whole is overkill, as suggested just use the algorithm for walking the edge of the bitmap. I have done this in the past (unfortunately I had no luck digging up my code, though in any case it's QD based and generates a region) and it's quite easy - maybe 15 lines of code tops. potrace docs explain how it works. If you wanted an OS-supplied, one-liner to replace CalcCMask - well, you're out of luck. You will have to do some work. If     CGImageCreateWithMaskingColors is inadequate, then vectorizing the outline of your bitmap and using it as a clipping path is how you need to solve it - one way or another. potrace does 99% of this as its first step, the remaining 1% is to take its internal list of vectors and convert them to a CGPath. Too hard? So write your own vectorizer that walks the edge and generates a CGPath directly. Same difference in my view. --GC

