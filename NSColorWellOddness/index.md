---
layout: page
title: NSColorWellOddness
---

I have an app with a custom view (subclass of NSView) and I am drawing something in it.  I have a inst var 'color' of type NSColor.  I have this code as the action of the two color wells on the window (tags 0 and 1):

    
- (IBAction)setColor:(id)sender
{

	int whichWell = [sender tag];
        NSColor *newColor = [sender color];

        [newColor retain];
	
	switch(whichWell)
	{
		case 1:
			[bgcolor release];
			bgcolor = newColor;
			NSLog(@"going after bgcolor, tag = %d", whichWell);
			break;

		case 0:
			[color release];
			color = newColor;
			NSLog(@"going after color, tag = %d", whichWell);
			break;
	}
	
	[self setNeedsDisplay:YES];

}



...if the NSColorWells are set, in IB, to "Continuous" then the color selection works fine and when the setNeedsDisplay triggers drawRect to fire, the proper color is there.  But it's slow.  My view is complex, what I am drawing.  Why would it not work without "Continuous" on?  Is there a inColorSelection event like there is for inLiveResize that I can tell my view to draw nothing with, while Continuous color selection takes place?  Any thoughts appreciated.


blakespot

----

Do I need to get into setTarget: and setAction:, etc. here?  I am working out of one NIB here.  I am at an utter loss.

Thanks.



blakespot

----

Just a guess (but it's better than nothing I suppose), have you tried setting the continuous flag for NSColorPanel? I suggest you do that and keep the color well on continuous. Try calling this after the application has started up:

    
[[NSColorPanel sharedColorPanel] setContinuous:NO];


-- RyanBates

----
I skimmed the docs, made a test project to confirm the idea and when I got back you'd already posted what I was going to. Hate it when that happens! ;-)

-- RyanStevens

*I was going to do the same, but I was too lazy. Looks like the lazy bird gets the worm today. ;) Thanks for confirming it though. -- RyanBates*

----


Success!!!  Thanks so much, here!  I live and learn and, hopefully, grow a stronger Cocoa developer each day.

(EDIT) and a few minutes later:  It _does_ let me slide all over the NSColorPanel's color selection area, mouse down, and see the color updated only in the top of the panel, not in the constantly redrawing view...but when I let up on the mouse, it _does_redraw the view.  So If I slide around and end up on a green, and let up mouse - I must wait for the drawRect to redraw the view before I can click again in the color selection area and get anoter color going.  Far better than it was...but still - would be nice if it did not attempt to apply that color to the view/redraw the view until I close the NSColorPanel.  But again - far better than before!

blakespot

----

If you really want to only apply the change when you close the color panel, then it's probably best if you make this explicit. Register for a window close notification on the color panel and update from there.

-- MikeAsh

----

You may also want to consider an "Apply" button next to the color well, because I don't know if users expect the color to take effect when the color panel is closed. I don't know of any other applications which work this way. -- RyanBates

