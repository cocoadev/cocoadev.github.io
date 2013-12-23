---
layout: page
title: NSProgressIndicatorInMultithreadedDrawing
---

blakespot

I have a view that takes a long time to draw.  I want to thread part of drawRect so that I can setup a NSProgressIndicator to let the user know something is rendering.  I am new to threading.  Any suggestions?  Thanks.



----

Multi-threading drawing in the UI isn't really easy and often has weird side effects.  However, you could make your drawing into an image and then set the display to that image when you are done (or, similarly have it display the partially-drawn image and have it update periodically).  Take a look at ThreadWorker to get an idea of how to do communication between threads.  This would be the best way for your drawing thread to notify the main thread of your progress (for updating the NSProgressIndicator and refreshing the display).

--JeffDisher

----

If you just want an NSProgressIndicator to spin while you're drawing, just call     [progIndicator setUsesThreadedAnimation:YES]; and then start it in the beginning of your drawRect: and stop it at the end.  -- Bo

----

Bo,  Are you sure this works?  I tried this initially, and the thing would not spin - I think just one instant before the drawRect finished its 4 second render, the thing moved one tick.  Maybe I was doing something wrong?  -blakespot

----

What Bo suggests will work.  I have done it before, too.  You need to have it set to indeterminate, though, since you will have no way of telling it how far along you are while your thread is busy.

----

however, if you can figure out how to buffer your threads, then the determinate progress bar is easy; see ProgressIndicatorInToolbar

----

I am handling this in code from my custom view's .h and .m.  In my .h I have:

    
IBOutlet NSProgressIndicator *progInd;


In my custom view's .h awakeFromNib I have this:

    
[progInd setUsesThreadedAnimation:YES];


And I have the for loop that makes up the time consuming part of my drawRect wrapped in these:

    
[progInd startAnimation:nil];

 . . .

[progInd stopAnimation:nil];


...and when that part triggers, I get the std 4-5 seconds it takes to render the view, and right when the view renders, the progress bar which was just sitting there still moves just a touch.  And it also is always visible, dispite my having unchecked "show when idle" or some such in IB.  And yea the NSProgressIndicator is hooked up to the outlet *progInd;

update: in awakeFromNib, telling it not to animate (in code) when idle does hide the bar (now always, as it's not moving ever!).   Any suggestions?  Thanks.  --blakespot

----

You probably want:
    
[progInd setIndeterminate:YES];

with your:
    
[progInd setUsesThreadedAnimation:YES];


----

I did set that in IB, but I will try it - still, it looks like a barber pole, so I think that's ok.  Why won't the thing move during my calc loop in drawRect though??  I don't think I am missing anything.

Someone suggested that I need to make that calc in drawRect its own thread, but that seems more than needs to happen, and that loop does plotting of pixels in Quartz, and I hear you don't want to access AppKit classes in a subthread.  Ugh.    --blakespot

----

In my experience, what you are telling blakespot will work is not possible.  I don't think anyone who is saying so has actually achieved this in their app.  

For him to have a rolling NSProgIndicator in his main window active while his drawRect is running, he must indeed subthread the portion of his drawRect that does the calculation and that means "plotting" to a buffer rather than using the native drawing tools in that subthread.  Not for the feint of heart, sadly.

I would like to see an example showing that this is not the only way to get this done.

----

I have successfully got a (circular) NSProgressIndicator to run using the methods described while I was doing non-threaded setup work (in -makeWindowControllers). You can see it if you go to my page and follow the link to SCatalogical, but it's done all the time. The background work takes around twelve or fourteen seconds and the progress indicator behaves as expected. So should blakespot do his drawing in another method? Is -drawRect screwing up the view hierarchy so the progress indicator won't update? �BrentGulanowski

----

I have had this work but not while the busy thread is drawing in the same window.  There might be some sort of issue with that, which is causing problems.  I had this work perfectly while the main thread was blocked on network I/O, for example.

Maybe you shouldn't do this heavy computation in the drawRect method of the view (especially because that method may be re-invoked by some other operation and won't give you control over what is going on).  You could try drawing this into an NSImage before you actually need the drawRect to be invoked.  That way the ProgressIndicator should be able to update with threaded animation while you draw into the image.  When the drawing is complete, then you could set the view to hold the image (you could even make it a NSImageView to simplify this further).

Could that work?

--JeffDisher

----

Maybe that's the way to go...  And actually, I commented out the actual drawing code in drawRect and only ran the calculations that generated the x,y coords in that function, and the calcs run so fast I could see no difference between running 10,000 iterations or 100,000 - all basically instant (< 1/4 second).  So the lag here is entirely the use of Quartz to plot that many tiny circleInRects (aka "points"!).  

How simple a matter is it to plot into an NSImage and move that to the view vs. drawing straight into the view?  Another though would be to use OpenGL.  I spoke with a guy who also wrote a fractal app (far more impressive than mine) which was taking lots of time to draw.  He used an OpenGL view to plot in and found a 100x increase in speed!  The thing is, though, looking at example code - OpenGL is entirely foreign to me and I've had positive reinforcement of having built this app up while understanding and making the right choices in most places.  Shifting to OpenGL right now would be a steep curve I don't want to tackle as yet, to maintain me feel of solid accomplishment.  

Hmm.     -blakespot

----

I've done the "render a complex display to an image" thing, and got great results.  It was a Three-D histogram that the user could click and drag on various values.  The rendering was a lot of small polygons with variations in shading to give a cool slab-like effect, but was pretty slow (especially on my older TiBook).  The user tracking performance was awful.  I rendered it into an image, and kept that around (since the underlying data did not change often, and if it did it was because of a pretty heavy-weight computational process).  For doing mouse tracking, it just blasted the image into the view then drew whatever user feedback was appropriate on top of the image.  

What I did was keep an NSImage instance variable.  In drawRect:, if the image is nil or a flag is set, I do all my drawing into it.  I then draw the image into the view.  If the data underlying my view changed, it just sets the flag and does setNeedsDisplay:YES.  By deferring the image creation until  drawRect:, a bunch of data changes can happen without incurring the extra expense if I had re-created the image whenever the data changed.

++MarkDalrymple

----

Ok right now I have all kinds of IBActions triggering a redraw when vals change by doing a setNeedsDisplay:YES.  If I did the draw into an NSImage thing, I'd - what - have a function off somewhere that did that drawing and then from there call drawRect by way of a setNeedsDisplay?  I guess the current setNeedsDisplay's would change to a call to the new draw-into-image function which, alone, would trigger a setNeedsDisplay, right?  Can I use same drawing methods I use to draw into view to draw into Image?

Thanks.   -blakespot

----

If you send the NSImage a     lockFocus before your drawing code and then     unlockFocus afterwards, you can use the same drawing instructions as you would with     drawRect:

(at least I seem to recall that being how it worked back when I was playing with this stuff)

--JeffDisher

----

Something like this (typed into the web browser, not tested)

    
- (void) drawRect: (NSRect) bounds
{
    bounds = [self bounds];
    if (image == nil) {
        image = [[NSImage alloc] initWithSize: bounds.size];
        [image lockFocus];
        [self timeConsumingComplicatedButReallyCoolDrawFunctionInRect: bounds];
        [image unlockFocus];
    }
    [image compositeToPoint: bounds.origin
           operation: NSCompositeCopy];

    // then do any other drawing on top of the image
} // drawRect


Then in other methods that change your display, you'd release the image and set image to nil.  Or if that seems wasteful, use a BOOL flag to know when to regenerate the image.  In my particular case I didn't have to worry about resizings since by (the client's) design the view wouldn't ever resize spontaneously due to a user event.  If your view can be resized, then you'll want to watch for bounds changes and then destroy the image (which then you'll recreate at the proper size at the next drawRect:.

++MarkDalrymple

----

Thinking about this...why is it faster to use the same drawing routines to draw into an NSImage and composite it onto the view vs. drawing right to the view??

Thanks ---blakespot

----

Because each drawRect: method invocation targeted at the screen has overhead, i.e. it is drawn in an offscreen buffer which needs to be transferred to the graphics card and the part of your window will then need to be redrawn, going through clipping, redrawing superviews if you do not return YES in isOpaque etc.

When drawing to an NSImage none of this overhead exists, until you "flush" your bitmap. So you limit the overhead to each time you flush, rather than each time you draw.

