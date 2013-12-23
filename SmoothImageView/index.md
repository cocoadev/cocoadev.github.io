---
layout: page
title: SmoothImageView
---

One of the things about NSImage that completely mystifies me is why on earth there's no way to tell it to use the highest image interpolation possible. You know how icons in the Finder and in the Dock scale really beautifully, i. e., the icons are smooth at all sizes? Now try loading an icon file into an NSImage, displayed in an NSImageView of course, and scaling it up and down. It looks awful at any size other than 128x128, 64x64, 32x32, etc.

So I decided to write my own image view. It was very simple; it just composited the properly-scaled image into its view area. Scaling the image using the highest interpolation was the only tricky part. On hindsight, I suppose I could have put that code outside of the image view and just used a regular NSImageView. Maybe I'll do that next time.

Anyway, here's the basic image scaling code, which I figured out with the aid of someone else's sample code on one of the Cocoa mailing lists (I found it in the archives at mamasam.com, but I don't remember who the author was, so thanks whoever you are!):

    
// assume oldImage already has been created containing the full-size image
NSImageRep *sourceImageRep = [oldImage bestRepresentationForDevice:nil];
newImage = [[NSImage alloc] initWithSize:NSMakeSize(48,48)];
    // those numbers are the desired width and height of the scaled image in pixels
  
[newImage lockFocus];
{
[[NSGraphicsContext currentContext] setImageInterpolation: NSImageInterpolationHigh];
[sourceImageRep drawInRect:NSMakeRect(0,0,48,48)];
    // those numbers should match the numbers above
}
[newImage unlockFocus];
         
[oldImage release];


And there you have it. Basically, a new blank image is created and the image representation of the original full-size image is obtained and drawn into the new image at a smaller size using the highest possible interpolation. This code should work under almost any circumstances, but I'm sure I'd like to hear about any situations in which this code didn't work.

Good luck!

-- Jared White

Editor-in-Chief, The Idea Basket
http://www.theideabasket.com

P. S. This is the first time I've posted anything on CocoaDev! Yay!

