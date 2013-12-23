---
layout: page
title: HowToConvertNSCachedImageRepToNSBitmapImageRep
---

I need to convert NSCachedImageRep to NSBitmapImageRep. Is there any API or method to do so?

Thanks,

----
Create an NSBitmapImageRep of appropriate size.
Add it to an NSImage instance.
lock focus on it.
composit the cached image rep.
unlock focus.
----
That's incorrect.  When you lockFocus on an NSImage, that creates an NSCachedImageRep (if necessary), throws away the other image image reps, and draws into the cached rep.  An NSCachedImageRep is basically an offscreen window where you can draw, and from which it is very efficient to copy bits to another window.  To draw directly into an NSBitmapImageRep, you need to use +[NSGraphicsContext graphicsContextWithBitmapImageRep:] and +[NSGraphicsContext setCurrentContext:].  See http://developer.apple.com/samplecode/Reducer/listing16.html for an example (search the page for setCurrentContext:).

