---
layout: page
title: AdvancedStringDrawing
---

I am trying to move into some more advanced drawing in my views, and one of the first things I want to create is a mirrored text effect, similar to the mirroring of the video chat image in iChat, where it looks like a reflection. iTunes does this also in several places in the Music Store with images. (Yes, I know iTunes is Carbon). I am looking for a way to do it with text, just reflect it below the drawn text. How can I do this? I haven't worked with CoreGraphics too much. Any help is much appreciated. --LoganCollins

----

You might want to look into NSAffineTransform.

----

I can't seem to get anything to flip or mirror with NSAffineTransform. Is there something I'm missing?

----

Try scaling by -1

----

Okay, I have a transform going, but how do I apply it to a string that's to be drawn? (Actually, it'll be an attributed string.)

----

Well, you could     -lockFocus on an off-screen canvas (an NSImage?), draw your screen (don't forget to     -unlockFocus), then transform the whole thing. I haven't done this directly, but that's the approach I'd take.

----
From the "Basic Drawing" programming topic, linked at the top of the NSAffineTransform docs: *You can also apply a transform to all of the current drawing operations using the concat method. This method appends the transform�s matrix to the transformation matrix stored in the current graphic context.* So you'd just concat the transform, then draw, and what you draw will be transformed.

Thanks! That worked perfectly. I couldn't understand why it didn't show up when I scaled the y axis by -1, but then it hit me that it would mirror not only the string's rect, but also its coordinates in the view, so it was drawing off the top  of the screen. --LoganCollins

