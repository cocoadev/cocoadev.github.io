---
layout: page
title: StretchingTextToFitAnySize
---



I'm trying to 'watermark' images of any size with an arbitrary string. For now, this watermark doesn't have to be rotated, etc, it just needs to go across an General/NSRect of my choice. Because this image is generated in real time, I need a very efficient way of drawing an updated watermark atop the image as it's scrolled, sized, etc.

The approach I tried was to create (and cache) an General/NSImage of the (attributed) string's size, with the string drawn into it. I'd then use General/NSImage's -drawInRect:fromRect:operation:fraction: method to draw it in place. The *problem* with this approach is that the text is badly pixilated. This didn't really surprise me.

I saw another example of increasing the font size and checking the attributed string's size until the string is bigger (then of course use the last 'good' value), but this strikes me as **terribly** inefficient since it'd be called in -drawRect: 

I can't believe this is so difficult to do and I *must* be missing something obvious. If anyone has done this before, I'm not above accepting code handouts, but I'll settle for a good solid push in the right direction, though. :-)

----

In my experience, doing a binary search on the font size is acceptably fast. Basically, start out with a reasonable minimum and maximum size, such as 1 and 128. Take the average, see if the result is too large or too small, and set the average as your new min or max depending on the answer. Continue this until the min and max are close enough to each other, such as within one point, and then use the min to draw your string. For this example it will only take seven passes to find the right size, and when I implemented this it was visibly instantaneous on a General/PowerBook G4/1.5. You can do even better if you cache the result and only redo the search if the font, string, or rect changes.

----

Easy. Use General/NSBezierPath's appendBezierPathWithGlyphs:count:inFont: to make a vector path of the required text. You can use General/NSLayoutManager to get the glyphs for a given string. Then scale it, rotate it, etc using an General/NSAffineTransform to your desired size, then stroke and fill it as appropriate. By cacheing the path you should find performance is very good. This approach means you don't have to care about the font size - just scale the text to fit the rect you have. Being a vector, it will scale smoothly without pixellation.

This page has some more concrete detail on this approach: General/NSBezierPathGlyphEncodings 

 --General/GrahamCox
