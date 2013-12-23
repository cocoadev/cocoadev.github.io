---
layout: page
title: ExtendibleWindow
---




Here's some code that allows you to extend a window to reveal some hidden items - such as when you hit a disclosure triangle for example. This is not terribly tricky, but there are a few things to watch, since usually it's the bottom edge you need to extend, which of course is where the items in the window measure their Y position from.

    
- (void)			extendWindowBy:(int) amount
{
	// extends the window vertically by the amount (which can be negative).
       //This doesn't disturb the positioning or
	// size of any of the views, whatever their autosizing parameters are set to. 
	
	NSView*			cv = self window] contentView];
	[[NSView*			view;
	NSEnumerator*	iter = cv subviews] objectEnumerator];
	[[NSRect			fr;
	NSPoint			fro;
	
	[cv setAutoresizesSubviews:NO];
	self window] disableFlushWindow];

	while( view = [iter nextObject])
	{
		fro = [view frame].origin;
		fro.y += amount;
		[view setFrameOrigin:fro];
	}
	fr = [[self window] frame];
	
	fr.size.height += amount;
	fr.origin.y -= amount;
	
	[[self window] setFrame:fr display:YES];
	
	[[self window] enableFlushWindow];
	[[self window] displayIfNeeded];
	[cv setAutoresizesSubviews:YES];
}


This is the core of it - it just extends or shrinks the window's bottom edge by the given amount, leaving all the current subviews undisturbed. It could probably most usefully be a category on [[NSWindow, though in practice I usually just add this code to the necessary NSWindowController, which is why it refers to [self window], rather than just self - so if you make it a category you'll change that. Note that this works even if the window can be resized by the user - the views will autosize correctly according to the sizing set up in IB.

I usually couple this with another method that I can link to the disclosure control which tracks the window's current state (expanded or normal) and hides and shows the additional view(s) that the extension reveals. This code isn't very generic since it depends exactly what you're revealing, but it'll give you the general idea.

    
- (void)			setExtended:(BOOL) extend
{
	// extend the window and show/hide extra items
	
	if ( extend != _extended )
	{
		_extended = extend;

		[_extraItemView1 setHidden:!_extended];
		[_extraItemView2 setHidden:!_extended];
		
		if ( _extended )
			[self extendWindowBy:kWindowExtensionAmount];
		else
			[self extendWindowBy:-kWindowExtensionAmount];
	}
}



Here, _extended is a BOOL data member which tracks the current state, and _extraItemView1 and ...2 are view members that I'm showing and hiding. kWindowExtensionAmount is a constant I arrive at by inspection - a cleverer and more general approach could be to measure the height of the view being revealed and use that, then you can set it up in IB and it'll "just work".

Submitted by GrahamCox.

