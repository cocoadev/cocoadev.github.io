---
layout: page
title: NSRulerViewCreatingGuides
---

I have a class which implements guide lines as a layer of a drawing. That's all fine, debugged, working. I would like to be able to create guides in the manner of Photoshop et. al. by dragging them off the ruler. So I have a scrollview with NSRulerViews showing, and my main view is a client of both rulers. I pick up the client message - (void) rulerView:(NSRulerView*) aRulerView handleMouseDown:(NSEvent*) theEvent; , which allows me to detect the initial click in the ruler. I can use this event to create the guideline, but what now? I want to drag down into the main area of the view and have the guide track the mouse. My guide object does that part as long as I can get a mouseDragged event to it, but I can't see how to get a mouseDragged event out of the NSRulerView. Any idea how to do this?
----
OK, I figured it out myself - it's easy. All I had to do was to implement my own short modal tracking loop as long as I don't see a mouseUp event. This loop keeps control until I stop dragging the guide. Here's my code, based on the example in the cocoa docs "Handling Mouse Events in Views":

    

- (void)				rulerView:(NSRulerView*) aRulerView handleMouseDown:(NSEvent*) theEvent
{
	// this is our cue to create a new guide, if the drawing has a guide layer.
	
	GCGuideLayer* gl = self drawing] guideLayer];
	
	if ( gl )
	{
		// add h or v guide depending on ruler orientation
		
		[[NSPoint p = [self convertPoint:[theEvent locationInWindow] fromView:nil];
		
		if ([aRulerView orientation] == NSVerticalRuler)
			[gl createVerticalGuideAndBeginDraggingFromPoint:p];
		else
			[gl createHorizontalGuideAndBeginDraggingFromPoint:p];
	
		[gl mouseDown:theEvent inView:self];
		
		BOOL keepOn = YES;
 
		while (keepOn)
		{
			theEvent = self window] nextEventMatchingMask: [[NSLeftMouseUpMask | NSLeftMouseDraggedMask];
 
			switch ([theEvent type])
			{
				case NSLeftMouseDragged:
                    [gl mouseDragged:theEvent inView:self];
                    break;
				
				case NSLeftMouseUp:
                    [gl mouseUp:theEvent inView:self];
                    keepOn = NO;
                    break;
            default:
                    /* Ignore any other kind of event. */
                    break;
			}
		}
	}
}


