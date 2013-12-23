---
layout: page
title: AnimationDrawingFlicker
---

I've been learning Cocoa recently by writing a simple screen saver involving moving objects rapidly around the screen (think Planets from NextStep).  One thing I've noticed in my implementation is that the individual planets seems to flicker between where they were in the previous frame and where they've moved in the current frame.  I know that this is generally solved in other graphical systems by double buffering, but I can't seem to figure out how this works exactly in Cocoa.  Can anyone point me in the right direction?  Thanks!

----

One approach that I've used involves using one or more NSImages as off-screen buffers.  Whenever you do any drawing, you draw into the NSImage and then when you're done you composite the NSImage onto the screen.

The first thing to do (in your view's initWithFrame: method) is make an NSImage the same size as your view and make sure the image is empty (or black, or whatever you want), like this:

    
    buffer = [[NSImage alloc] initWithSize:frame.size];
    [buffer lockFocus];
    [[NSColor clearColor] set];
    [NSBezierPath fillRect:frame];
    [buffer unlockFocus];


Then, whenever it's time to step one animation frame, you do your drawing into the buffer and then mark your view as "dirty" so the AppKit will redraw at the end of the event loop

    
    // this makes all drawing happen in the image instead of the view
    [buffer lockFocus];
    // now do some drawing, e.g. composite another image into our view
    [myImage compositeToPoint:myLocation operation:NSCompositeSourceOver];
    // we're done drawing
    [buffer unlockFocus];
    // this will make a redraw occur
    [self setNeedsDisplay:YES];


Now all you need to do is implement drawRect: to copy your buffer to the display:

    
- (void)drawRect:(NSRect)rect {
    [buffer compositeToPoint:[self frame].origin operation:NSCompositeSourceOver];
}


You can extend this technique by using multiple NSImages.  You might for example have one NSImage that contains a static background, and begin each animation-step drawing by copying that into your main buffer image, then compositing your other graphics on top of that.

Hope this helps!

// JackNutting

----

Thanks for the advice, Jack!  I'd figured out a solution of that nature just after posting this page... but it didn't quite work exactly.  It did make things much better, but I still get a little ghosting just behind the moving object.  I my solution, I'd used the NSCompositeCopy operation rather than the one you suggested.  I'll try NSCompositeSourceOver and see if that makes a difference.

Thanks, again!
Andrew

----

I tried that technique too, but something strange happens. Because my picture has alpha, wherever my picture is drawn, it destroys the black background and reveals the background of the ScreenSaverView, which is just like any window. Is the compositing the source of the problem ?

-- Trax

