---
layout: page
title: CompositingVsDrawing
---



What's the difference between compositing an image and just plain drawing it?

Another question: I have a custom NSView, and the graphics in it scroll up, but it continues to draw onto the window beyond the bounds of the rect. How do I avoid that?

----

If you limit your drawing to inside your -drawRect: method, your drawing should be  clipped so you can't draw outside of it.  If you want to draw into your view from anywhere else, remember to call -lockFocus before you start and -unlockFocus after you're done.  -- Bo

----

I encountered a similar problem and have reported my solution here: CustomViewDrawsGarbageOutsideRect.

----

"Compositing" refers to operations combining pixel images (bitmaps/pixel maps).  The window server does a lot of compositing of the bitmaps of the windows, for instance.  Generally the compositing operations take an NSCompositingOperation, which defines how the source and destination pixels are combined.  ++MarkDalrymple

----

You can use composite..., draw..., and/or dissolve... methods when drawing an NSImage.

From the Apple docs on Drawing and Images:

*Images displayed using the various composite or dissolve methods, such as compositeToPoint:operation: or dissolveToPoint:fraction: , have the horizontal orientation of the base coordinate system; they can't be rotated or horizontally flipped. *<some text deleted>

*Images displayed using the draw methods, such as drawAtPoint:fromRect:operation:fraction: , have the orientation of the current coordinate system; they are rotated and scaled as defined by the view into which the image is drawn.*

I ran across this distinction when trying to scale an NSImage proportionally to fit into a custom view. When I used composite... the NSImage was always full size, but it scaled and shifted as expected when I used draw...

-- Mara

----

Which is faster? Compositing or Drawing? I have a preview box in my current project, which lays image previews from QTMovie files on top of each other (the NSImage is grabbed via [movieObj posterImage], which works so-so). However, the background color is drawn, and these images are all drawn using drawInRect:, when the NSView's drawRect: method is called, and the performance is crap. The background color is picked with a color well that is attached to the view using bindings. As the user drags in the color picker, the color changes, but because the drawRect: method is called every time that color changes, it's pretty darn slow.
Which drawing method is faster/more efficient, and is there a way that I can get the images into a smaller footprint or reduce the performance hit when drawRect is called? The end user may end up having 20 or more of the images being drawn in the view.

----
Cache your multiple images. Draw them all into a single blank image using [un]lockFocus, then draw that one image into your view when needed. You avoid scaling and blending your 20 images every single time something changes.

