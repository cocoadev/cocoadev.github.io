---
layout: page
title: DrawingAParabola
---

I am trying to draw a parabola (y = x*x) in my ScreenSaverView subclass. It draws the white box fine if I leave out the     for loop and the 2 lines after that.If I leave them in, it only gives me a black screen. What am I doing wrong?

    - (void)animateOneFrame
{
	int i;
	[[NSColor whiteColor] set];
	[[NSBezierPath bezierPathWithRect:[self bounds]] fill];
	path = [NSBezierPath bezierPath];
	for(i=0;i<[self bounds].size.width;i++) { 
		NSPoint pt;
		pt.x = (float) i;
		pt.y = (float) i * i;
		[path lineToPoint:pt];
	}
	[[NSColor blackColor] setStroke];
	[path stroke];
	[self setNeedsDisplay:YES];
	return;
}


----

Remove the     setNeedsDisplay: line, it's not necessary and is causing your (probably empty)     drawRect: method to be invoked, overwriting your drawing here.

----

Thanks, but the same thing still happens. (You're right about my     drawRect: routine)

*According to Apple, all of your drawing code should be in your     drawRect: method, and you should call     setNeedsDisplay: in     animateOneFrame if that's how you are going to draw it. However, since you do all of your drawing beforehand, you might be able to leave     animateOneFrame empty and move everything else to     drawRect:. That being said, your problem is likely the absence of calling     lockFocus on your view (in this case     self), which means you are drawing off in space somewhere. --JediKnil*

That's not true. According to the docs:

*It is guaranteed that the focus is locked when this method is called, so subclasses may do drawing in this method.*

The problem lies elsewhere.

----

I found my bug. If I insert a     [path moveToPoint:NSZeroPoint]; before the     for loop, it all works! Thanks for your help!

