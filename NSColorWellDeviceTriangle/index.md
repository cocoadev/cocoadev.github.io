---
layout: page
title: NSColorWellDeviceTriangle
---

Hello,

I'm working on an app that deals heavily with color, and I would prefer to use the NSColor device color space.  But I don't want that grey triangle to show up in the corner of all of my color wells and dragged colors.

So short of using the calibrated color space which I know gets rid of the triangle, is there any way to prevent the triangle from showing up when using the device color space?

Thanks!

----

Well... there's no public method to do that. But it certainly draws somewhere in the drawing hierarchy of NSColorWell, so I would suggest that you try overriding methods one at a time (- drawRect:, - drawWellInside:, and - _drawBorderInRect: would be the three) to do nothing until the triangle no longer appears. The last method you overrode would be the one that draws the triangle, so once you've found the culprit, you can reimplement whatever that method is responsible for yourself. I have some code that draws an appropriate swatch complete with white/black triangle on transparent colors if you need it. On the other hand, it might be best to just leave the whole thing alone: Apple put that triangle there for a reason, and it's not generally good mojo to change the appearance of standard controls without good reason. -- AndyMatuschak

----

Hey Andy,

Thanks for the response.  After my first message I did figure out that overriding drawWellInside: for NSColorWell did the trick.  However, this still left the triangle on colors as they are being dragged (any idea what method controls the drawing for a dragged color from [NSColorPanel dragColor:withEvent:fromView]?).

But I also had the same thought you did: Apple probably put it there for a reason.  All things equal I would rather use the calibrated color space.

The problem is when I draw an object on the screen as, say, RGB: 255,0,0 in the calibrated space, and then sample the color of the object later with NSReadPixel, the color I get back is not 255,0,0 because the calibrated space makes a number of transformations on the original color causing it to change.

The device space works as intended with no transformations and keeps my raw RGB values, but the only drawback is the big ugly triangles all over my color wells and dragged colors.

So that's the reason I'm trying to just remove the device color triangles and work in the device space.  Would you (or anyone) know of any work-arounds for my issues with the calibrated color space?  Would it be a bad idea to just remove the triangles and work in the device color space?  I'd really appreciate any thoughts or advice.

Thanks so much!

----

Being a developer on a graphics program myself, I can say with pretty good confidence that you want to use calibrated colors in just about all cases; otherwise, your users end up with colors that don't look like what they really picked. When you read with NSReadPixel, you get a device color. All you have to do to get it back to the original 255, 0, 0 is do [NSReadPixel(point) colorUsingColorSpaceName:NSCalibratedRGBColorSpace]. But that's slow... so I'd suggest that you not use NSReadPixel in the first place. Try just using an NSBitmapImageRep (or unsigned char*) for your data model instead of an NSImage. That'll prevent conversion into an NSCachedImageRep and will allow you to directly read and write data on the image. Keep in mind that if you do that, however, you'll have to worry about premultiplying your alpha (and undoing that on export). In any case, if you decide you do want to use the device colors but don't want, you might try overriding - dragImage:at:offset:event:pasteboard:source:slideBack: to call super with an image of your own. Presuming that NSColorWell uses that method. -- AndyMatuschak

----

Hi Andy,

Just wanted to say thanks a ton for all of your suggestions so far.  As I said, I'd love to use the calibrated space, but for some reason I'm having a problem with what you described above.

I draw a rectangle to my view using [NSBezierPath fillRect:rect] after setting my color using [[NSColor colorWithCalibratedRed:1.0 green:0.0 blue:0.0 alpha:1.0] set] (sets color to RGB 255,0,0).  I then use [NSReadPixel(point) colorUsingColorSpaceName:NSCalibratedRGBColorSpace] exactly as you mentioned above to grab the color I just painted to my view.  Except that the color that I get back from this operation is RGB: 233,23,5 - not 255,0,0 as I originally painted to my view.

Now I know a number of conversions and transformations are taking place when converting between calibrated and device and back again, but is there any way to get my original 255,0,0 back from the screen in the calibrated color space?  Have you had any luck with this?  Because no matter what color I paint to the screen in calibrated space, I always get a different color back from [NSReadPixel(point) colorUsingColorSpaceName:NSCalibratedRGBColorSpace].

Thanks again for all of your insight.  I really, really appreciate it!

----

*squint* That's interesting. The conversions between calibrated and device are complicated, but except for rounding errors that crop up every once in a while, they're pretty much reversible. I have noticed that if you lockFocus on an NSImage, it becomes cached, right? Which means it's probably on the video card's memory banks... which I suppose implies device. This is sorta why we recently stopped using NSImages to store data for Pixen. Try using an NSBitmapImageRep and then filling rects using the bitmapData pointer; that might be more reliable. -- AndyMatuschak

----

Hi Andy,

I'll definitely give the NSBitmapImageRep a try...but are you sure that the conversions are reversible?  The only reason I ask is because if you:

- open the cocoa color picker
- switch to the RGB sliders
- set the sliders to RGB: 255,0,0 in "Generic RGB" space
- now just switch to "Device RGB" and then switch back to "Generic RGB", and the sliders become RGB:233,23,6, not RGB: 255,0,0 as originally entered

This leads me to believe that the conversions might not be reversible since Apple's own color picker can't seem to convert back and forth between them correctly...Do you get similar results?

