---
layout: page
title: ScaleDrawingWithWindowResize
---



I have a custom view in a window and I would like to have the drawing in the view scale as the window is resized.  How might that best be achieved?

----

Maybe you could use something like this simplistic view-zooming behavior in a custom view subclass, where the zooming behavior
is coupled to the before-and-after frame sizes of your view relative to the resizing operation

Live redrawing of a complicated view might be messy...

It's also gonna be up to you to figure out how to trap your resizing events, unless someone else wants to help.

One freebie (or less) per person per day is my limit

Disclaimer: this is fairly nasty code - for instance, it is probably taking on
waaaaaaay too much for an accessor method...I was pretty inexperienced when I wrote this code, but it worked for me
and I never fixed it.

If somebody has better idea then we will both learn something.

    
// Setter accessor for a scaleFactor ivar in your zoomable view
- ( void ) setScaleFactor: ( float ) aFactor     // you get this parameter by using your before and after sizes
{
	if ( scaleFactor != aFactor )          // scaleFactor is the last known scale relative to some unit size
	{
		float delta = aFactor / scaleFactor;
		scaleFactor = aFactor;
		**[ [ self contentView ] scaleUnitSquareToSize: NSMakeSize( delta, delta ) ];**      // here is the rescaling
	}
	
	[ nzView setFrame: [ [ self contentView ] frame ] ];                 // nzView is an outlet to the non-zooming superclass of the
	[ nzView setNeedsDisplayInRect: [ nzView bounds ] ];                   // zoomable view that this code came from
}

