---
layout: page
title: HowToSaveImageFiles
---

After changing the effects of an image by reducing its size or saturation, how to save that altered image as an image file?

----

If you're working with an NSImage, ask for a representation (which is given to you as an NSData) and write that out as you would any NSData. This information becomes immediately apparent when you read the documentation for NSImage.

----
Actually in my application I am using CIImage. Then how to save the CIImage into a file?

----

Simply wrap the CIImage in an NSCIImageRep, add that to an NSImage, and go from there.

----

I can save the image and open it in Preview application when I don't apply any filters for the image. But when I applied the filters(like CIBlur, CIDistortionEffect etc) to the image I could able to save the image, but I could not able to open the image in preview application.

