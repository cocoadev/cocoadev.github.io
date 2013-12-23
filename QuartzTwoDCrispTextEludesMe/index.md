---
layout: page
title: QuartzTwoDCrispTextEludesMe
---



Hi all,

I have a subclassed NSView, and I want the user to be able to resize a word of text somewhere inside there using a slider. This alone is not difficult. But there are niggly things thatare affecting the way I am trying to approach this.

First of all, I need to know the length of the string I am drawing so that I can fit it perfectly (resize it) within another on screen element.

I started with just NSAttributedString, which allows me to change the point size, and has a 'size' attribute I can use. However, as I slowly resize the text bigger and smaller, the letters jiggle about a little, the kerning changes pixel by pixel, It's quite bad, and I can't accept that.

Second I tried the Quartz 2D drawing routines, (CGContextShowTextAtPoint) which is better than the NSAttributedString method, in that the scaling is smooth and rock solid, however the text gets very fuzzy at small point sizes and I can't find a way to improve this (tried the setImageInterpolation on my context to high - makes it even fizzier at low point sizes).

So what's the solution? Is diving into ATSUI going to be my help here? 

Any other suggestions?

Thanks for your help!

----

Have you tried rendering the text into an image during the resize, scaling that, then restoring the text at the end?

