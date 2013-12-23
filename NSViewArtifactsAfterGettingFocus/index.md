---
layout: page
title: NSViewArtifactsAfterGettingFocus
---



I've subclassed an NSView to draw an image across it.
This works fine but everytime I press a button or anything else that is in the view (I have dropped a NSSearchField on top of the view in IB so it's in the view)
then lines and other artifacts apper around those objects...

http://www.frikkinsoft.eu/screenshot2.png

I've just subclassed -drawRect...

    

- (void)drawRect:(NSRect)rect
{	
	NSImage *img = [NSImage imageNamed:@"BottomBar.tif"];

	[img drawInRect: rect  
		   fromRect: NSZeroRect 
		  operation: NSCompositeSourceOver 
		   fraction: 1.0];
        [img release];
	
	NSBezierPath *path = [NSBezierPath bezierPathWithRect:rect];
	[path setLineWidth:1];
	[[NSColor darkGrayColor] set];
	[path stroke];
}


----

The rect passed in represents the portion of your view that needs to be redrawn - you're assuming it's the whole view.  You can get your whole view's location by sending [self bounds].

The section with the path, for example, draws a gray box around the invalidated area each time.  That's not what you want.

