---
layout: page
title: ResizeViewToFitContent
---

I'm trying to get an NSImageView to resize itself to fit the graphical content inside it. I can't think of any way to do this without writing a method to generate an NSRect from the dimensions of the graphic, and I would really like to avoid going through something like that if there is already a good solution. If you happen to have a thought on this subject, please add it below. If you are sure nothing like this exists, I'll be happy to write it up and add it in, but please let me know.

-- QetiPadgu

----

I wonder if the original poster will see this since he posted it 6 months ago!!

    
+ (NSRect)centeredFrameForBounds:(NSRect)bounds imageSize:(NSSize)imageSize {
    float viewRatio = NSWidth(bounds) / NSHeight(bounds);
    float ratio = imageSize.width / imageSize.height;
    float dx = 0.0f, dy = 0.0f;
    if (viewRatio > ratio) dx = (NSWidth(bounds) - NSHeight(bounds) * ratio) / 2.0f;
    else dy = (NSHeight(bounds) - NSWidth(bounds) / ratio) / 2.0f;
    return NSInsetRect(bounds, dx, dy);
}


I looked around for this function too, but I couldn't find one. Since I saw this page I thought I would put my solution here.  --zootbobbalu

