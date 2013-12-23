---
layout: page
title: BorderlessWindowResize
---



I thought this would only take 5 mins, but I've spent hours on it now... and it's really having a great time frustrating me. I am just trying to write some resize code for a borderless window (without any normal NSWindow chrome such as title bars or resize jobbies - hence the reason I am re-writing the resize code).

My code so far allows the user to drag the window around by clicking and dragging anywhere on it, and also to resize the window should the user click and drag in the lower-right 20px by 20px square. At least that's what's supposed to happen... I've had terrible trouble dealing with Cocoa's upside-down coordinate system as I attempt to pin the top-left hand corner as I resize vertically. Horizontal resizing is working great, and I am also resizing quite fine vertically if I just accept the cocoa coordinate system, and have the window pinned bottom-left. 

As soon as I make the last step and attempt to pin the window /top/ left (by changing the origin.y of the frame, then calling setFrame) the vertical resizing is flickery and the mouse-pointer is not locked to the position on the window where the user clicks to start the drag.

I hate to offload a big bumf of code like this, but this is what I've got in my NSWindow subclass:

    
- (void)mouseDragged:(NSEvent *)theEvent
{
	NSPoint currentLocation;
	NSPoint newOrigin;
	NSRect  screenFrame = [[NSScreen mainScreen] frame];
	NSRect  windowFrame = [self frame];
	
	// 1. Is the Event a resize drag (test for bottom right-hand corner)?
	if (mouseDownType == PALMOUSEDRAGSHOULDRESIZE) {
		// i. Remember the current downpoint
		currentLocation = [theEvent locationInWindow];
		
		// ii. Adjust the frame size accordingly
		windowFrame.size.width = initialFrame.size.width + (currentLocation.x - initialLocation.x); // Works 
		float heightDelta = (currentLocation.y - initialLocation.y); // Calc how much to add/chop off the window
				
		windowFrame.size.height = initialFrame.size.height - heightDelta; // Seems to resize ok when pinned to bottom left
		windowFrame.origin.y = initialFrame.origin.y + heightDelta; // Should Pin top left corner? Kind of does, but resize is weird and flickery!
		
		// iii. Set
		[self setFrame:windowFrame display:YES]; // Boom/Flicker/Grrshhch
	}
    else {
		...
		(Code for just moving the window... works... so far)
		...
		
	}
}

//We start tracking the a drag operation here when the user first clicks the mouse,
//to establish the initial location.
- (void)mouseDown:(NSEvent *)theEvent
{    
    //grab the mouse location in global coordinates
	initialLocation = [theEvent locationInWindow]; // ivar
	initialFrame = [self frame]; // ivar
	
	// What should a drag event do, if started from here?
	if (initialLocation.x > initialFrame.size.width - 20 && initialLocation.y < 20)
		mouseDownType = PALMOUSEDRAGSHOULDRESIZE;
	else
		mouseDownType = PALMOUSEDRAGSHOULDMOVE;
}




I *bet* I'm just being stupid aren't I?

----

I *was* being stupid. I botched up my screen-to-window coords in the above. My rectified code can be seen at BorderlessWindow.

----

Thanks for this code -- it was a great example for something that I needed to do as well.

I modified the code so that it would behave like the real resizing control (it won't freak out if you move the mouse further to the left or above where the window started and will respect the minimum size of the content view) and thought I'd share it:

(this code modifies the code that is already in the mouseDragged method)

    

		// Get the minimum size of the content pane
		NSSize minSize = [self contentMinSize];

		// make sure we don't make window smaller than content view's minimum size
		if ( currentLocation.x >= minSize.width ){
			windowFrame.size.width = initialFrame.size.width + (currentLocation.x - initialLocation.x);
		}
		
		if ( currentLocationOnScreen.y <= (initialFrame.origin.y + initialFrame.size.height - minSize.height ) ){
			float heightDelta = (currentLocationOnScreen.y - initialLocationOnScreen.y);
			windowFrame.size.height = (initialFrame.size.height - heightDelta);
			windowFrame.origin.y = (initialFrame.origin.y + heightDelta); 
		}


