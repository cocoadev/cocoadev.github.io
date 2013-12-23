---
layout: page
title: UsingNonQuartzDrawingInACocoaApp
---

 

I have a fractal generator plotting simple fractals in my Cocoa app using standard Cocoa drawing methods (General/NSCircleInRect or somesuch as I recall) to plot individual dots.  For each dot I must setup a small circle and then stroke it.  It would seem that Carbon style or old General/QuickDraw style (not sure just what to call it - non Display PDF) would be far faster for plotting dots in a view.   http://goo.gl/General/OeSCu

Where can I look to see how to keep my app the same - but just change from plotting with Quartz to using a faster method like I describe?  Thanks.

Here is a sshot of my app:   http://www.blakespot.com/images/fracttoy.jpg

blakespot

----

How about allocating your own bitmap and draw directly into this one, surely that will be the fastest way. Then you can write it (with Cocoa) to the window 1-5 times pr. second.

----

How is this done?  Is there example code that I might take a look at?  Thanks.

blakespot

*I don't know about examples, but the most lowlevel graphics API is Quartz, and this is from the documentation:*

*A bitmap image is a rectangular array of pixels, with each pixel representing a single point in an image. To create a Quartz image, you call General/CGImageCreate or General/CGImageMaskCreate with parameters that describe the precise format of the bitmap. To draw the image, you use General/CGContextDrawImage.*

*Google for the two methods with site:developer.apple.com and you will find the documentation for the functions.*

----

If you want quickdraw then use General/NSQuickDrawView.

- General/FranciscoTolmasky

----

There is a subtle but important difference between using Cocoa's drawing functions, and using Quartz itself . If I understand the General/MacOS X architecture properly, both Cocoa and Carbon (aka General/QuickDraw) are high level libraries which make use of the lower-level Quartz (aka General/CoreGraphics) library to draw to the screen.

Assuming you do really want to use Quartz from within a Cocoa application, here are a few pointers.

From a programmer's point of view, Cocoa sets up the Quartz environment (a "General/CoreGraphics context") before invoking your view's     -(void)drawRect:(General/NSRect)aRect method.

The General/NSGraphicsContext class is your friend in this situation: a call to     General/[[NSGraphicsContext currentContext] graphicsPort] will return a General/CGContextRef for the current Quartz graphics context, which you can then pass to all the Quartz drawing routines.

I can't seem to recall exactly what functions you'd use to draw individual pixels. In fact, given that General/CoreGraphics is designed to allow you to manipulate both bitmap and vector graphics contexts, you may need to roll your own (the idea of setting the colour of individual pixels makes no sense if the graphics context doesn't use pixels). Alternatively, calling General/CGContextFillRect() with a rect whose height and width are exactly 1.0 might fit the bill if speed is not of the essence.

Apple's Quartz documentation is available at http://developer.apple.com/documentation/General/GraphicsImaging/Quartz-date.html

Of course, another alternative, as mentioned above, would be to create your own General/NSImage offscreen, and write to it yourself. I know I've read discussions of how to do this, probably here on General/CocoaDev. Can anyone jog my memory?

- tom

----

From my experiences, General/OpenGL in an General/NSOpenGLView is very fast, especially these days with Quartz Extreme.  And yes, General/OpenGL DOES do 2D, and it does it extremely well.  It's what I have used for IFS fractals in the past.  You may want to give it a try.  -- General/BrianMoore

Yeah, what Brian said. Ask about General/OpenGL techniques. You basically have to break down your drawing into points, lines, and polygons/triangles. If you can do that, you can draw into **any** General/NSView. If General/OpenGL has the tools, it will beat any and all other techniques for performance. � Brent

*But he is (AFAIK) drawing only points, so General/OpenGL would be a bad idea. The only thing he could do was draw into a texture and upload it and then draw it with General/OpenGL -- but the drawing into an image and drawing this is easier and about the same speed.*

There is a way to use a shared texture that you can draw in that might be a lot faster if the data is in the right format. Conversion is the slowdown, not copying over the bus, which is fast. --Brent

Not to be annoying or anything, but I kinda disagree with that.  General/OpenGL can draw individual points really quickly and really well.  You just pass GL_POINTS to glBegin() instead of passing it GL_LINES, GL_QUADS or whatever.  You can even control the point size with glPointSize(float).  But wait, there's more!  GL will actually antialias points if you set it up right.  Said ability is accessed by glEnable-ing GL_POINT_SMOOTH.  I think other things probably have to be in line for the smoothing to actually show up, not sure, but that's irrelevant for this discussion for now.  Just wanted to put that out there.  -- General/BrianMoore

*Well, yes, it may draw fast -- but can it draw faster than drawing them to a local bitmap? to draw each General/OpenGL point you need to call     glVertex2i(x, y) (or a variant thereof, and most likely prefix it with a call to     glColor3f()), which goes through a dyld stub and which needs to add the point to a stack (and later transfer it to the GPU, over the bus), which I would think is slower than simply doing:     bitmap[y*width + x] = color -- then you later need to transfer the bitmap over the bus, and that might be what makes the General/OpenGL solution faster (because you do not transfer non-plotted pixels with General/OpenGL). -- but, were you in a situation where the points had to be plotted only once, and then rotated, scaled, zoomed etc., then General/OpenGL would certainly be the way to go (as you would not need to transfer the points on each frame).*

With a vertex array you can transfer everything with one bus.
----
"Cocoa Programming" includes Chapter 13 which among other things describes Drawing Points and Rectangles, Drawing Individual Rectangles, Drawing Lists of Rectangles, and Drawing with Bitmaps.  The sample code for Chapter 13 is available at http://www.cocoaprogramming.net/Downloads.html.

However, having said that, I think General/OpenGL is the best bet for modern systems with General/OpenGL hardware.  Even 3 years ago, GL hardware could not compete with General/CPUs for most 2D frame buffer types of drawing.  The GL hardware vendors were focussed on making Quake texturing run fast and were ignoring primitive operations like point drawing.  Today, General/OpenGL hardware is much faster than general purpose General/CPUs even for point drawing, and in fact, the entire fractal generator could probably be implemented as a pixel shader and run completely on the GPU without using a single cycle on the CPU.
