---
layout: page
title: IPhotoLikeButtonStrip
---

Hi,

I'm trying to create a button strip / toolbar like that use in the latest iPhoto versions (underneath the photos) for my app. A little research has hinted that overriding the NSToolbar would be bad as it would need use of several private methods. So far I have opted to create an NSView and attach a series of custom NSButton classes to that view. The result so far seem good, except that when clicked on, the button draws a thick grey line along its bottom edge. Not only does this look pretty awful, but it stays drawn even when the button is released.

I have set the border and bezel types to (what I think) are 'do nothing', but can't seem to get rid of this line. Could anyone point out where I'm going wrong?

Code for my custom button is:

    

// MyToolstripButton.m
//
- (id)initWithFrame:(NSRect)frameRect
{
	if( self = [super initWithFrame:frameRect] )
	{
		[self setBordered:NO];
		[self setImagePosition:NSImageAbove];

		// Setup the strip button's action cell
		self cell] setBezelStyle:[[NSShadowlessSquareBezelStyle];
		self cell] setHighlightsBy:[[NSNoCellMask];
		self cell] setFont:[[[NSFont systemFontOfSize:10.0]];
	}
	
	return self;
}

//
// Method to create a MyToolstripButton and attach it to given NSView
- (MyToolstripButton *)toolstripButtonInView:(NSView *)aView
									   frame:(NSRect)aFrame
									   state:(BOOL)state
								  continuous:(BOOL)continuous
									   image:(NSImage *)image
									   title:(NSString *)title
{
	MyToolstripButton *button	= [[MyToolstripButton alloc] aFrame];

	// Setup the button
	[button setContinuous:continuous];
	[button setState:state];
	[button setImage:image];
	[button setTitle:title];
	
	// Add stripbutton to view
	[aView addSubview:button];
	[button release];

	// Return the newly created button
	return button;
}



Thanks
Chris
--
www.chrisblunt.com

----

I, too, had a similar problem at one time. I had created an NSView subclass, and instaniated in IB, then added the buttons. My subclass was set to draw a black line at the top to look like that grey bar in the podcast browser on iTunes. However, each of the buttons were also displaying a black line. Why? They were subviews of the main view, and it was giving them the same black line trait of the view. You should try just making regular buttons, set them to show only the icon (make the button transparent with only an icon), and add them in IB, overlapping the "MyToolstrip". From what I can tell, sometimes objects inherit the visual traits of their superviews. No idea why, that's just what I've observed. -- JasonTerhorst

----
Thanks Jason, that seems to have been the problem. I tried adding an extra subview to contain all my buttons within the toolstrip view, but that suffered the same problem. Turns out that it's only the drawing of the bottom border that's causing the problem, so for now I've stopped drawing it! I'll post back anything else I can find out.

Thanks,
Chris
--
www.chrisblunt.com

