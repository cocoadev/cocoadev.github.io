---
layout: page
title: VeryStrangeNSRectBug
---



I've used General/NSRect (via General/NSMakeRect) many times before without any problem. Until now. I have four floats that are the result of various calculations. The rect I'm creating is meant to be the bounds of an object drawn in a view, and as the view scales, so will the objects drawn within it. Here is the code I've worked with thus far:

    

- (General/NSRect)drawingBounds
{   
	float wratio = 800 / [view bounds].size.width;
	float hratio = 600 / [view bounds].size.height;
	float x = General/self xPosition] floatValue] / wratio;
	float y = [[self yPosition] floatValue] / hratio;
	float w = [[self width] floatValue] / wratio;
	float h = [[self height] floatValue];
	//h = h / hratio;
	float newh = h / hratio;
	
	//[[NSLog(@"wratio: %f, hratio: %f, x: %f, y: %f, wsize: %f, hsize: %f", wratio, hratio, x, y, w, h);
	General/NSLog(@"wratio: %f by hratio: %f, w: %f by h: %f, and odd number: %f", wratio, hratio, w, h, newh);
	
    General/NSRect drawingBounds = General/NSMakeRect(x, y, w, h);
	//drawingBounds.size.height = newh;
	//drawingBounds.size.width = w;
	//drawingBounds.origin.x = x;
	//drawingBounds.origin.y = y;
	
    return drawingBounds;
}



The "drawingBounds" method is for the rect of the object drawn in the view.
As you can see, I've been playing with and creating new variables for the height dimention. The correct resulting value is in "newh", and is correct when I run it and watch the "General/NSLog" output in the console. However, when I actually use "newh" instead of "h" in General/NSMakeRect, all of the other values change! "x" becomes a -0 value, as does "y" and "w", and the "newh" value becomes infinity ("inf"). These other values somehow change, even when I only substitute "newh" in place of "h" in General/NSMakeRect. How is it changing those other values, not to mention the fact that those other variables are already cast before newh is even declared!

----

At a glance, I can't find anything wrong with the above (though it'd be easier to understand without all the commented-out stuff, especially in the absence of syntax highlighting). I would recommend posting a bit of the code that's calling this method as well. Also, I see the     General/NSLog() call within the method ... do you call     General/NSLog() *after* this method returns and just prior to using it to see what it's like there? I'll assume you have, but ... better to confirm that assumption. :-)

----
It really pays to debug this stuff a little more thoroughly. Log all the inputs to each formula, and the outputs. See if they all make sense. Step through the thing in the debugger line by line and check all the variables to see if they still make sense. Right now you just have "a problem", and you've have no real idea where it's happening. Narrow it down to one line and then the problem should be obvious.

----
The line-by-line approach is a good idea, and I used General/NSLog calls after every line to see what was happening. I've checked the status of the view's bounds before the first line in the method, and the numbers are correct. However, when I use "newh" instead of "h" as the height param for the General/NSMakeRect, then the width bound from the view goes to zero, even though "newh" isn't a global var (none of them are - this is an General/NSManagedObject subclass), and I haven't changed any other values or variables.

----
Well, I fixed the problem, but it involved changing my approach. The method noted above is now simplified:

    
- (General/NSRect)drawingBounds
{   
	General/NSLog(@"ratio w: %f and h: %f", wratio, hratio);
	
	General/NSRect drawingBounds = General/NSMakeRect(General/self xPosition] floatValue]/wratio, [[self yPosition] floatValue]/hratio, [[self width] floatValue]/wratio, [[self height] floatValue]/hratio);
	
    return drawingBounds;
}


I've added the global variables wratio and hratio to the object class, and the view iterates through the objects to set their ratio values before actually telling them to draw themselves in the view. BTW, my code is part of an effort to add on bits of functionality to the [[GraphicBindings example by mmalc ( http://homepage.mac.com/mmalc/General/CocoaExamples/controllers.html ). So far, I've added mouse dragging, scaling, etc. This code is part of my latest effort - allowing the objects to be scaled in proportion with the view. If you would like to help with it, or see my progress, email me. -- General/JasonTerhorst

----

With respect, can we make this page more useful by having you include what the problem actually was, or is this page forever doomed to be in General/MailingListMode, completely useless for future searches? Say it ain't so!
