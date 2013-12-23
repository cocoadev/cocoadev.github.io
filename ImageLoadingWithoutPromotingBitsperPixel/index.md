---
layout: page
title: ImageLoadingWithoutPromotingBitsperPixel
---



I'm loading textures for OpenGL and up until now I've relied on 32 bit PNG images with alpha channels. Now, to save a little texture memory, many of them can be drawn additively as grayscale 8-bit images -- thereby saving some texture memory.

Eventually I will use SDL_image -- and likely this problem will go away, or be different, but right now I'm using NSImage and NSBitmapImageRep to load my images, and here's the rub: 8-bit grayscale PNGs get loaded as 24-bit. I can understand why, from a color-space perspective. Cocoa promotes the image to a mode where standard 32-bit color compositing algorithms work beautifully and quickly.

But I don't want 24 bit images! I want 8-bit images.

Any ideas?

--ShamylZakariya

----

*Editor's note: This question was first posted on CocoaDiscussions in Oct 2004. Has a solution arrived in the interim?*

