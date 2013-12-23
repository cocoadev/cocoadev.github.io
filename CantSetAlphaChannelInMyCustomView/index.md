---
layout: page
title: CantSetAlphaChannelInMyCustomView
---



Here's the background...
I have created a subclass of NSView inside a subclass of NSWindow.
The window uses NSBorderlessWindowMask and is set to Opaque:YES so it is effectively invisible.
The view is a rounded rect, painted black with 0.5 transparency.

Here's the problem...
I have written some code in the drawRect method to produce an evenly spaced grid of line.
The problem is that even though I have set the alpha of the NSBezierPath to 0.5, they exhibit really odd behaviour:
The vertical lines draw at alpha of 1.0. 
The horizontal lines draw at an alpha of 1.0 until the last few which progressivly get more transparent!

I am at a loss. 
Here's some code, now I have the formatting right... Let me know what you think...
More info: The 'rect' comes IB where I have set the size of the Window and View bits.

    
	//New code to draw the lines grid
	int rectWidth = rect.size.width; //gets the width of the custom view in pixels
	int rectHeight = rect.size.height; //gets the height of the custome view in pixels
	int divider = 25; //how many divisions do you want the page divided up into? set it here
	int countX = rectWidth/divider; //calculates how many 7 pixel spaced lines are in the view width...
	int countY = rectHeight/divider; //...and likewise for the height
	//now we know the size of the view and how many times to draw a line in the x & y axis of the view
	//the nextstep (see what I did there! ) is to draw the lines...
	
	NSBezierPath *gridLine = [NSBezierPath bezierPath]; //Create an NSBezierPath
	NSColor *gridColor = [[NSColor whiteColor]colorWithAlphaComponent:0.5];//Set the color and the alpha channel for the lines
	[gridLine setLineWidth:1]; //set the line width
	[gridColor set]; //set the line colour
	//now the loop we run through to draw the lines going vertically
	int i;
	int coordX = (divider - 1);
	for (i = 0; i <= countX; i++) {
		[gridLine moveToPoint: NSMakePoint(coordX,0)];
		[gridLine lineToPoint: NSMakePoint(coordX,rectHeight)];
		[gridLine stroke];
		coordX = coordX + (divider - 1);
	}
	//and now we'll do it again horizontally
	int j;
	int coordY = divider;
	for (j = 0; j <= countY; j++) {
		[gridLine moveToPoint: NSMakePoint(0,coordY)];
		[gridLine lineToPoint: NSMakePoint(rectWidth,coordY)];
		[gridLine stroke];
		coordY = coordY + (divider - 1);
	}


----
Don't reuse your NSBezierPath. Each time you do a moveToPoint:/lineToPoint: combo you are *adding* to the path. Stroking the path doesn't clear it, so by the time you get to the last iteration in your loop, you have a path which contains many line segments, and you draw them all. The first lines appear opaque because you've been drawing them over and over again. The last ones get drawn fewer, all the way down to the last one which gets drawn only once, so it appears to work.

Either move the stroke outside of the loop, or make a separate path each time through the loop to stroke it, and your problem should go away.

By the way, opaque is the *opposite* of transparent, so you probably want to setOpaque:NO on the window.

----
Thank you! It worked like a dream...
Great suggestion and well explained I understand now. Plus you're right about the opaque thing, it was late when I posted is my only excuse! ;)

----
If you (or anyone else) wishes, see if you can refactor this page to be generally useful to future generations. Or else delete it. :-)

Yes, I too would be curious to see the code that worked. Thanks!

