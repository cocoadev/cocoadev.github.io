---
layout: page
title: ResizingAViewsSuperView
---



Hey all, I'm lost on this...  I've got a simple subclass of NSView.  It does nothing spectacular but I wanted to figure out how to drag a view.  Mission acomplished.  Then I wanted to limit the drag to the superviews viewable area.  Again, managed to get it worked out.

The next thing was to try to resize the height of the superview when the dragable view went beyond the superview's current frame.  So if it was 10px above the top of the viewable area of its superview, the superview would resize itself by 10px to acomidate the needed extra space.  Unfortunately the superview just does nothing.  I cant figure out where to go next.  Is there something besides the superview's frame I need to worry about?  If someone could spare like 30 seconds to see if there's any glaring omition in this code I'd really appreciate the favor.

FYI: startDragPoint is set on mouseDown and currentPoint is set on mouseDragged.  I believe both are set reliably based on the fact that limiting the dragable area to the superview seems to work correctly.

    
-(void)moveDragView
{
	//	Find Difference between current X,Y & the X,Y of starting mouse click
	float difx = currentPoint.x - startDragPoint.x;
	float dify = currentPoint.y - startDragPoint.y;
	
	//	Find X,Y of the origin of dragable view before the mouse drag began
	float startOriginX = startFramePoint.x;
	float startOriginY = startFramePoint.y;
	
	//	Set frame that we want to drag to the frame of the current view
	NSRect dragRect = [self frame];
	
	//	Get the frame of the superview so we know where to end the drag bounds.
	NSRect superRect = self superview] frame];
	
	//	Find the max X,Y positions for our dragable view
	float maxDragX = superRect.size.width - dragRect.size.width;
	float maxDragY = superRect.size.height - dragRect.size.height;
	
	//	Determine new X position of dragable view (self's frame)
	dragRect.origin.x = startOriginX + difx;
	if(dragRect.origin.x < 0)
	{
		dragRect.origin.x = 0;
	}
	if(dragRect.origin.x > maxDragX)
	{
		dragRect.origin.x = maxDragX;
	}
	
	//	Determine new Y position of dragable view (self's frame)
	dragRect.origin.y = startOriginY + dify;
	if(dragRect.origin.y < 0)
	{
		dragRect.origin.y = 0;
	}
	if(dragRect.origin.y > maxDragY)
	{
		//dragRect.origin.y = maxDragY;
		
		/*
		 Rather than stopping the drag, I want to resize the content area of the super view.
		 Unfortunately the viewable area of 'self' does not resize.
		 The dragable view is simply allowed to be positioned outside of the superview.
		*/
		
		superRect.size.height = superRect.size.height + (dragRect.origin.y - maxDragY);
		[[self superview] setFrame:superRect];
	}
	
	//	Reposition Dragable view
	[self setFrameOrigin:dragRect.origin];
	
	//	Redraw self & super views
	[[self superview] setNeedsDisplay:YES];
	[self setNeedsDisplay:YES];

	
	//[[NSLog(@"\nMoved X: %f\nMoved Y: %f", difx, dify);
}


Thanks, CliffPruitt

----

Well, still not quite solved but I did realize what the big problem was.  I was using an NSBox as a quick way to to display a background in the subview so I could see if it resized.  Stupid me didn't think to take into account that that would be the superview of the object I'm dragging so when i was referencing [self superview] it was referencing the NSBox, not the view I actually wanted to resize.  Good thing I only spent like six hours making no headway because of that stupid mistake.  Sheesh... :-)

- CP

----

Is there any more to add to this? I'm looking to do the same thing (resize a superview to encompass all subviews when a subview is dragged past the superview's bounds). Does anybody have anything to add to the code above (ie, is this a good approach or should another be taken)?

