---
layout: page
title: DrawingReflections
---

This is a idea that I've been looking at for a long time. What would be the best way to draw reflections of an image (ala Keynote, Pages, iWeb, etc.)? From what I have read through there's nothing in the Cocoa drawing methods to facilitate this, so one would have to drop down to Quartz, which I am partially familiar with but have no problem using. Basically how I see it, one would draw the image (using the normal Cocoa calls), and then draw the image again directly below and flipped from the original. The problem I'm having is with the linear gradient fade-out. I have used CGShading to draw gradients in the past, but I can't seem to figure out how to draw the image to fade out downward and then have whatever has been drawn behind it show through (instead of just doing a transparent-to-white gradient on top of the image). Also, my target platform is 10.3+, and therefore CoreImage is not an option. Any help with this topic is greatly appreciated. --LoganCollins

----

One way is to draw a gradient into a similar sized NSBitmapImageRep, where the colours of the gradient have the appropriate alpha values you want. Then composite both images together to the screen. The image will be multiplied by the alpha gradient, et voila! the effect you seek. You might also be able to draw the gradient into the same image using the appropriate compositing mode, but then that would permanently alter the source image.

----

If your images are of a fixed size (e.g. you always display them at 320x240 pixels) you can create a gradient image of that size in Photoshop or similar. Have the gradient go from completely transparent at the top to completely opaque black at the bottom. Then, when compositing your reflected image, use an NSAffineTransform instance to flip your image-to-be-reflected, and draw the gradient image atop that. (Of course, if you don't restore the graphics state before drawing the gradient image, it'll be flipped just like the image-to-be-reflected.)

----

This NSImage category does what you are looking for: http://bithaus.com/2006/11/05/nsimage-reflection/

----

To answer the question precisely, check out the CoreGraphics ImageMask functions. Use it with a gradient and you'll get that nice reflection effect.

