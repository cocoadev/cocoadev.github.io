---
layout: page
title: SetOpaqueWindowProblem
---



Hi guys,

I'm trying to make a transparent textView and it works when called in     awakeFromNib, but when it is called from an IBAction a textView changes only its color but not transparency. It changes transparency only when I switch to Finder and back (or to another app or just another window in this very app).

I tried to call     display,     update,     invalidateShadow on everything: window, textView, scrollView, window's contentView, and also     NSApp updateWindows without any luck. What can be wrong here?

    
- (IBAction)setTransparent:(id)sender
{
	NSColor *backColorWithAlpha = [NSColor colorWithCalibratedRed:0.5
		green:0.5
	       blue:0.5
		alpha:0.5];
	[textView setBackgroundColor:backColorWithAlpha];
	[scrollerView setDrawsBackground:NO];
	[mainWindow setOpaque:NO];
}

