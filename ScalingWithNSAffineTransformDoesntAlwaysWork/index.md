---
layout: page
title: ScalingWithNSAffineTransformDoesntAlwaysWork
---



I'm trying to create a thumbnail image of a view by drawing a scaled version of it into a new NSImage, but drawing subviews don't get scaled in the process:

    
- (void) drawRecursively:(id)View withScale:(float)scale{ 
	
	// draws a view and its subviews into the focused rect. Probably not the best or most effecient way, 
	//	but it works and is the fastest.
	NSArray *subviews = [View subviews]; 
	int i; 

	// set scale and proper origin point
	NSAffineTransform *transform=[NSAffineTransform transform];
	[transform scaleBy:scale];
	[transform translateXBy:[View frame].origin.x yBy:[View frame].origin.y];
	[transform concat];
	
	[View drawRect: [View bounds]];
	for (i = 0; i < [subviews count]; i++) { 
		NSView *subview = [subviews objectAtIndex: i]; 
		[self drawRecursively:subview withScale:scale]; 
	}

	// reset origin point
	[transform invert];
	[transform concat];
}


When I call this to draw the scaled version of the view (in my case, a webview's documentview), the subviews (mostly buttons and textfields) are still drawn full size, though the width does seem scaled (they appear clipped).

Is this a bug in the way the system scales things, or am I not doing this right? If I dont' scale it, everything looks as it should, so it's only when it  gets scaled that something isn't right. (I ran this under Panther, but I did notice that under Tiger, no subviews seem to be display at all - weird.)

I could simply forego drawing any of the subviews, since they're usually so small that they really don't show in the final output, but I'd rather keep them if I could. 

-Seb

----

The easiest way to accomplish this would be to create a full-sized NSImage from your view hierarchy using one of the many techniques available to do that, and then scale your NSImage *after* you've filled it up with your view. Trying to scale and then draw is going to make your life a lot more difficult.

**True, but doing that creates a crappy image since it doesn't anti-alias. Doing it scaled crates a softer image more appropriate for a thumbnail, so this is really the only way.** 

You certainly can get antialiasing when you scale an NSImage, so that seems like an odd objection.

The antialiasing is not the same though. It will be uglier to scale the bitmap than to render at the correct resolution from start. Slightly depending on content, obviously. But also note that if you scale down a lot (to less than perhaps 25 %), the difference will be hardly noticeable and you should get away with bitmap scaling.

If you get an NSImage in PDF form using     -dataWithPDFInsideRect: and then scale it, it should look perfect, since the generated PDF is (hopefully) going to contain lots of vector data that will scale nicely. This is probably going to be significantly slower than something like     -initWithFocusedViewRect:, however.

Another possibility is a new drawing redirection API in Tiger, which allows you to ask a view to draw itself into an NSBitmapImageRep. (Directly calling     -drawRect: as in the code above is extremely unsupported.) I don't see offhand a way to scale the drawing, but it might be possible. See the section called "NSView Drawing Redirection" in the AppKit release notes. I highly recommend the PDF approach, though, unless speed is extremely crucial.

*Getting a PDF from the view is the best way, however this doesn't always work well for a webview's document view since it returns the way it would look if it was printed, instead of the way it actually looks on screen currently, and I haven't found a way to force it to do that.*

