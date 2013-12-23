---
layout: page
title: ButtonWithThreeStatesAndTwoAlternateImages
---

How is it possible to make a button have 2 alternate images to be able to cycle through 3 images by clicking the button?

Should I try to subclass NSButtonCell to obtain my custom images for every state (on, off and mixed)?

If the better solution is to create a custom view, could you tell me how I can handle mouse events and update the image?

-- wdyp.
----

If all you want is an image which changes to the next image every time you click on it, then try this:

Drag over an NSImageView in Interface Builder and create a subclass of NSImageView (I'm calling it ToggleImageView). Changed the NSImageView custom class to the new subclass you created (hit command-5 to bring up the panel). Use this code for your ToggleImageView class. Note: code has not been tested.

    

// ToggleImageView.h

@interface ToggleImageView : NSImageView
{
	NSArray *images;
}

- (NSImage *)nextImage;

@end

// ToggleImageView.m

@implementation ToggleImageView

- (id)initWithFrame:(NSRect)frame {
	self = [super initWithFrame:frame];
	if (self) {
		// You will need to change the names of these images
		images = [[NSArray alloc] initWithObjects:
					[NSImage imageNamed:@"firstImage"],
					[NSImage imageNamed:@"secondImage"],
					[NSImage imageNamed:@"thirdImage"],
					nil];
	}
	return self;
}

- (void)dealloc
{
	[images release];
	[super dealloc];
}


- (void)mouseDown:(NSEvent *)theEvent
{
	[self setImage:[self nextImage]];
}

- (NSImage *)nextImage
{
	// Make sure we have at least one image to use
	if ([images count] > 0) {
		if ([images count] > [images indexOfObject:[self image]]) {
			return [images objectAtIndex:([images indexOfObject:[self image]]+1)]
		} else {
			return [images objectAtIndex:0];
		}
	}
	
	return nil;
}

@end



*Let me know if you get any warning or error messages.* -- RyanBates
----

I tried your code and it�s working perfectly.

There�s a little problem:

This custom view is supposed to replace an NSPopUpButton in my window. I used to programmatically bind the value "selectedIndex" of that NSPopUpButton to some value in a controller. When that value changes as another menu item is choosen, some transformations occur in the UI.

I added to your custom view a variable named "selectedIndex" so that I don�t have to modify the binding code.

NSLog informs me that "selectedIndex" does change when I click the view, but the UI doesn�t change and the bound value is not updated either.

What might be the reason for this different behavior?

-- wdyp
----

Just curious - why are you switching from a popup? I was about to suggest this as an alternative solution.
----

My popup has only 3 choices, I made the items� names very short but the popup is still very big and this popup appears several times in the interface, that�s why I�d prefer a small icon like the one in iCal for confirmed or cancelled events.

I don�t understand why two objects with a similar variable don�t produce the same result when bound�

Any help will be appreciated.

-- wdyp
----

Well, I found a solution but it�s very strange: I add to reverse the binding.

With the popup I had:
    
[thePopUp bind:@"selectedIndex" toObject:myModel withKeyPath:@"theKeyPath" options:bindingOptions];


And now I�m using:
    
[myModel bind:@"theKeyPath" toObject:thePopUp withKeyPath:@"selectedIndex" options:bindingOptions];


Why are my object and NSPopUpButton so different??

-- wdyp

