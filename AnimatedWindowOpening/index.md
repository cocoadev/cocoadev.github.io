---
layout: page
title: AnimatedWindowOpening
---




I've been attempting to create an NSWindow sublcass that, upon opening, starts as a small window (20x20 pixels) in the middle of the screen, and immediately animates the resize to it's proper size and location, and displays the proper content view, as designed in the nib.  To acheive the opposite effect when a window closes is as simple as overriding -[NSWindow close] to call [self setFrame:smallFrameInMiddleOfScreen display:YES animate:YES]; followed by a call to [super close]; delayed by the appropriate time time interval.  However, there doesn't seem to be any obvious analog for opening a window.  Any help would be greatly appreciated.  Thank in advance.


-Mike

----
Well, the obvious thing to do is to just do the opposite of what you're doing for the close: show the window, then set the frame with animation. But it's so obvious I'm sure you've tried it, so what happens when you do?

----
The problem I'm having is that there isn't any open: method to override... I've tried doing the obvious thing that you mentioned in displayIfNeeded, first checking a flag to see if the window's already open, but the funky and less then ideal result is that the window displays as the end result for a split second, before setting the frame to the smallFrame, and enlarging.

----

You don't *need* to override a method. Before you open your window, set its alpha value to 0.0, then use a timer to gradually move that up to 1.0.

----
Why would you override anything? Windows don't open by magic, they open because you told them to open. Tell them to resize while you tell them to open.

----
Yes, windows open because you tell them to, but I assume that if a generic subclass of NSWindow that animates while opening and closing is being created, ALL the code for animation should be in the subclass rather than having to handle it whenever a window is shown.

On that note, have you tried overriding makeKeyAndOrderFront?

Here's just something I threw together which solves the blip of the window initially showing with the original frame before performing the animation:

    
- (id)initWithContentRect:(NSRect)contentRect
				styleMask:(unsigned int)styleMask
				  backing:(NSBackingStoreType)bufferingType 
					defer:(BOOL)deferCreation
{
	if(self = [super initWithContentRect:NSMakeRect(60,60,20,20)
							   styleMask:styleMask
								 backing:bufferingType
								   defer:deferCreation])
	{
		mOriginalFrame = contentRect;
	}
	
	return self;
}

- (void)makeKeyAndOrderFront:(id)sender
{
	[self setFrame:mOriginalFrame
		   display:YES
		   animate:YES];

	
	[super makeKeyAndOrderFront:sender];
}


1. Override the init method, storing the original window's content rect in a variable and allowing the superclass to handle the rest.

2. Override the makeKeyAndOrderFront method, animating the size and location change and then allowing the superclass to handle the rest.

Hope that helps!

-TylerStromberg

