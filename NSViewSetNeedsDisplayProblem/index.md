---
layout: page
title: NSViewSetNeedsDisplayProblem
---



I'm having a bit of an odd problem with an NSView subclass. I have several other objects which frequently send -setNeedsDisplay:YES to this view. After a little debugging, I realized that every time I call -setNeedsDisplay:YES, something else is calling -setNeedsDisplay:NO before it ever gets around to drawing itself.

I inserted this code into the NSView subclass so I know when -setNeedsDisplay: is being called:
    
-(void)setNeedsDisplay:(BOOL)theBool
{
	NSLog(@"%@ got setNeedsDisplay:%@", self, theBool ? @"YES" : @"NO");
	[super setNeedsDisplay:theBool];
}


Now I also inserted an NSLog() into -drawRect: so that I would know if that was getting called. What I get in my logs is something like this every time one of my controller objects sends -setNeedsDisplay:YES. (And the view never draws itself.)
    
<MScrView: 0x15d1a790> got setNeedsDisplay:YES
<MScrView: 0x15d1a790> got setNeedsDisplay:NO


I'm positive that none of my controller objects are sending the second message. Is it possible that some NSView method is "automagically" deciding for me that it doesn't really want to display my view?

----

If you want to learn the root cause, you're going to have to launch a debugger and find it out yourself. It shouldn't be too hard, though. Just add a breakpoint to     -setNeedsDisplay, then look at the call stack when you hit it.

