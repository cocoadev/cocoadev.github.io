---
layout: page
title: PreferenceWindowQuestions
---



Ok so I have it "working" somewhat. I have 3 items in the toolbar. They have actions associated with them. I have an NSWindow being controlled by an NSWindowController (where the actions are and toolbar creation). I have 3 views. Clicking on the appropriate item results in displaying the proper view no problem. However. I wanted to auto-resize the window to fit the contents like iTunes and all those other great apps.

The problem comes if I just provide the frame for the view on the setFrame:display:animate message it moves the window to the bottom left corner of the screen. I want the window to remain in the same location. So I looked at the NSWindow page on here and there's a nice "resizeWindowOnSpotWithRect" message... no problem, however it seems to make my window smaller and smaller until it disappears ;) not cool.

I supply the view i want to display as the aRect in that function. 

Next on the agenda is making an item look "Selected" they all have a grey background in apps like iTunes.. how do I do that with my own toolbar? 

----

Ok figured out the selected items part. Now just gotta figure out what's so screwy with the resizing.

----

Here is the code I made for my new unreleased yet application:

    
- (void) slidePanelToSize:(NSSize)newSize {
	NSPanel *panel = [self window];
	NSRect panelFrame = [panel frame];
	float additionalHeight = panelFrame.size.height-panel contentView] frame].size.height;
	
	[[NSRect newFrame = NSZeroRect;
	
	float heightDiff = newSize.height + additionalHeight - panelFrame.size.height;
	float widthDiff = newSize.width - panelFrame.size.width;	
	newFrame.size.height = panelFrame.size.height + heightDiff;
	newFrame.origin.y = panelFrame.origin.y - heightDiff;
	newFrame.origin.x = panelFrame.origin.x-widthDiff/2;
	newFrame.size.width = newSize.width;	
	
	[panel setFrame:newFrame display:YES animate:YES];
}


It will resize your panel with top side fixed in place. Good luck.

-- DenisGryzlov

----

Here's my take on it. This code will resize the window based on the NSView object passed in, which is then placed inside an NSBox (you can use anything really) on the window. Using the NSBox lets you put additional objects (like a "Save" button) on the window along with the view objects you're switching around; if you don't need to do this, you might as well just use the window's contentView instead. Finally, this snippet also moves the window's origin if the resized portion lies outside of the screen.

    
- (void)_setMainView:(NSView *)aView;
{
	if ( aView == [viewContainer contentView] )
		return;
	
	NSWindow *window = [self window];
	NSRect windowFrame = [window frame];
	float extras = NSHeight( windowFrame ) - NSHeight( viewContainer contentView] frame] );
	float height = [[NSHeight( [aView frame] ) + extras;
	
	// Offset the yPos and set the height to fit the new view.
	
	windowFrame.origin.y += NSHeight( windowFrame ) - height;
	windowFrame.size.height = height;
	
	// Insert a temporary view during the frame change, or the old one will be
	// resized by the view container.
	
	[viewContainer setContentView:[[[NSView alloc] initWithFrame:NSZeroRect] autorelease]];
	[window setFrame:windowFrame display:YES animate:YES];
	[viewContainer setContentView:aView];
	
	// Move the window if it is outside the screen's frame.
	
	if ( NSMinY( windowFrame ) < 0.0f )
	{
		windowFrame.origin.y = 0.0f;
		[window setFrame:windowFrame display:YES animate:NO];
	}
}


I find it useful to hook my toolbar items to a single method, which looks at the selected toolbar item and sets the window's view and title accordingly.

-- MarcCharbonneau


----

Here's a class that will handle all of the necessary window resizing for your Preferences window. It also handles the toolbar for you.

http://www.mere-mortal-software.com/blog/details.php?d=2007-03-11

