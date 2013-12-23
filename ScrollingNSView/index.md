---
layout: page
title: ScrollingNSView
---


Sometimes, it's really boring to be a newbie.
I made a NSView (inside a NSScrollView) with IB. Then i'm dynamically adding subviews in it from top to bottom (see AutomatorLikeUI).
At this time everything is working good.
But obviously, as the Y axis is 'inverted', when a subview reach the bottom of the view, i had to deal with the scrollview.
Here what i wrote :
    
- (void)addBlocOfHeight:(int)heightValue
{
	yPos = yPos -heightValue;
	NSView *bloc = Bloc alloc] initWithFrame: [[NSMakeRect(0,yPos,[self bounds].size.width,heightValue)];
	[self addSubview: bloc];
	[plan addObject: bloc];
	[bloc release];
	
	if (yPos < 0)
	{
		NSRect bounds = [self bounds];
		NSSize frameSize = bounds.size;
		frameSize.height = topOfView -yPos; // or frameSize.height = topOfView +abs(yPos);
		[self setFrameSize: frameSize];
		[self needsDisplay];
	}
}

yPos is used to memorize the bottom of the last added subview and topOfView is just my view's y origin + my view's height.
So addBlocOfHeight: is increasing the framerect and allow me to scroll the view.
**The problem is it's adding space on the top of the view, not at the bottom** so the scroll shows me white at the of the view and the subviews are cut at the bottom...
What should i do? Do i have to completely change the way i did it? - StephaneDassieu.
----
You're expecting all of your views to be still relative to the top of your view. The problem is that the default autoresizing mask makes views be still relative to the bottom of your view. Try something like this right after you create the new view:
    
[bloc setAutoresizingMask:NSViewMinYMargin];

That should tell it to make the bottom space flexible and stick to the top, and then your code will hopefully work correctly.
----
Ahh i did find a simple way by myself (after carefully reading the Apple doc about **Drawing and Views** http://developer.apple.com/documentation/Cocoa/Conceptual/DrawViews/index.html).
It looks like i just have to **overide the isFlipped method** in my view subclass:
    
- (BOOL)isFlipped
{
	return YES;
}

**So the y-axis runs from top to bottom**. Easy!
- StephaneDassieu
----
That's a good way too. I personally prefer the autoresizing mask method, because I try not to flip views unless it's absolutely necessary, but that's just my personal preference. Your method should work just fine as well.

*yeah - the resizing mask is the right way to do this. flipping a view affects a lot more than just sizing behavior.* --> *Can you quickly explain what it affects?*

