---
layout: page
title: WhenToTurnOffAntiAliasing
---

I recently wrote a view which "magnifies" its contents by changing the frame of the view in relation to the view's bounds. Unfortunately, I need the view's contents to be magnified without any sort of interpolation or antialiasing (the view is in a raster drawing application).

My drawing code is something like the following:
    
- (void)drawRect:(General/NSRect)aRect
{
    General/NSGraphicsContext *currentContext = General/[NSGraphicsContext currentContext];
	
    [currentContext saveGraphicsState];
	
    [currentContext setImageInterpolation:General/NSImageInterpolationNone];
    [currentContext setShouldAntialias:NO];

    // draw everything of significance

    [currentContext restoreGraphicsState];
}


It doesn't work. Does anyone know how to accomplish this? I am thinking of migrating my rendering code to use General/OpenGL. Would this change aid the task at hand?

Thanks,

General/EliotSimcoe

----

Dear Eliot,

I don't think you need to change the graphics context if all you want to see is the individual image pixels at greater-than-pixel resolution. I have an application that reads tiff files into an General/NSBitmapImageRep, which is loaded into an General/NSImage, which sits in a custom subclass of General/NSImageView sitting inside an General/NSScrollView. To change the magnification I simply change the size of the frame of my custom General/NSImageView, which is automatically scaled to fit inside the General/NSScrollView. When you zoom in so that each pixel of the image takes up more than one pixel on screen, the individual pixels show up as individual blocks, without antialiasing except that which necessarily has to happen on the screen pixels lying on the edges of each image pixel. Is that the behaviour you wanted? If you want absolutely no antialiasing at all, even at the edges of the image pixels, you will have to align your image and screen pixels precisely, and only zoom in by integer amounts.

General/JulianBlow

----

Right, but that is because you are using the General/NSImageView class to do your drawing. For optimized General/CoreImage drawing this is not an option (A statement I left out of my original post. Sorry guys)

Thanks for your insight, I was not aware of this difference in rendering approaches between graphics context until you pointed it out.

General/EliotSimcoe

----

Some time ago I wrote a view that displays a General/CGImage and draws a crosshair mark - I don't remember the
exact solutions tried but the code ended up as:

    
General/CGContextRef ctx = General/[[NSGraphicsContext currentContext] graphicsPort];
General/CGContextSetShouldAntialias(ctx, useAntialiasing);


If you are using CG for drawing this might make a difference.

Hope that helps, General/BjoernKriews

Update: my code actually uses General/NSRectFill to do its drawing
