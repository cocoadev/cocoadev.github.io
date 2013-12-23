---
layout: page
title: ManuallyRedrawing
---




My problem is as follows : 
When switching presets in my program, I use [NSTextField setStringValue:@"next preset from memory"];
Problem is that I have a lot of text fields that I'm changing the values of.  When I change presets there is a noticable lag while the text fields are updated.  I am assuming that this lag comes from automatically redrawing every text field.  
Is there a way to turn this off and only redraw when I request it?

I tried using [NSWindow disableFlushWindow]; on the window that contains the text fields but obviously that does something different than I want.

Sorry to ask a basic question (I've looked everywhere on Google and know the answer must be out there if I knew how to phrase it right). I am new with GUIs and don't understand how it works.

----

Maybe try NSDisableScreenUpdates(); then NSEnableScreenUpdates when you're done. Not sure if it works, but it's worth a shot.

----
Don't assume, profile.

Your assumption is wrong. See     setNeedsDisplay: to understand why. This is the mechanism that Cocoa views use to invalidate themselves. It means that your views will all redraw together, even though you update them separately, so long as you do it during the same runloop cycle.

If you want to fix what's slow, first you have to find out what it is. Use Shark to profile your app, then once you know why it's slow you can go about making it better.

----

In addition to the excellent advice above, I'll also add (for clarity's sake) that it's more than likely the approach taken by your controller (the one that actually sets the text fields' values). If you find yourself forcing drawing anywhere else (by sending -display or -displayIfNeeded to any controls), it's a safe bet that's where your problem is. Also, if you use any delays (such as -performSelector:withObject:afterDelay:...) you should consider their impact on drawing. Finally, if you have a custom view anywhere (say, as a superview of the text fields you're talking about) that displays itself when some state changes, make sure you -setNeedsDisplay: rather than calling -display or -displayIfNeeded yourself. Forcing display may/will cause subviews to display.

I'd normally say PostYourCode but this kind of problem isn't likely to be contained in one or two methods that lend themselves to selective posting ...
'
----

Ok! Thanks for the advice - it helped me understand things and locate where my problem was, it wasn't where I thought.

I have an NSMatrix that "pops" up by setting the cells in it transparent.

    
- (void)transparentRow:(bool)test
{
	int x = 0;
	while (x < 6) {
		patch cellAtRow:posX column:x] setTransparent:test];
		[[patch cellAtRow:posX column:x] setBackgroundColor:unselectedColor];
		x++;
	}
        if (test) [mainWindow display];
}


so when I switch from transparentRow:NO to transparentRow:YES, it erases everything behind the [[NSMatrix.  mainWindow is an NSWindow so I can't call setNeedsDisplay on it, but I would like to wait until the end of the runloop for it to display, since I have to call some other displays afterwards.  Am I doing something which misses the point?

----

One point you're missing is that you should never have to do     [mainWindow display] at all. Making these changes to your cells should invalidate the view automatically and cause it to redraw when it's next required. If for some reason this doesn't happen, then the correct course of aciton is to do     [patch setNeedsDisplay:YES] which will invalidate it manually.

----

The problem is when I make the cells transparent again then they just color themselves as the background of the window, and there are objects beneath them that then need to be forced to redraw.

----

The draw order of overlapping views in Cocoa is undefined. There's no guarantee that a view, partially obscured by another view, will ever redraw. Heck, it could light on fire and it would be within the scope of "undefined."

----

This entire MailingListMode page is a towering monument to your need to read the Cocoa Drawing Guide and any associated materials referenced therein that you do not yet grasp. Each question you've posted is answered by that guide. There is no shortcut. There is no workaround. There is no prepackaged magic bullet that will make things work for you without understanding them. You **must** read the manual.

