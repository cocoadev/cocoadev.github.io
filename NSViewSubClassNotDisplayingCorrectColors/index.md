---
layout: page
title: NSViewSubClassNotDisplayingCorrectColors
---



This is driving me insane...

I've been fidling with this for hours and I can't figure out what I'm doing wrong.  I have a custom NSView that I've added properties to that allow me to specify if I want a border drawn on the top or bottom and the color to draw it in.  So I figured ok, easy enough, 0.0 three times in colorWithCalibratedRGB is black right?  Wrong.  It shows up on screen as a value of .9333 using Pixie and Digital Color Meter....it's like the NSView is automaticly changing alpha values or something... I have my alpha value set to 1.0 by the way.

Here's my awakeFromNib code:
    
[infoBar setBottomBorderColor:[NSColor colorWithCalibratedRed:0.0 green:0.0 blue:0.0 alpha:1.0]];
[infoBar enableBottomBorder:YES];


And here's the actual drawing:
    
- (void)drawRect: (NSRect)rect
{
	//Draw Gradient BackGround
	//[myGradient fillRect: rect angle: gradientAngle];
	
	//Init Pen
	NSBezierPath *penPath = [[NSBezierPath alloc] init];
	[penPath setLineWidth: 1];
	
	//Draw Top Border if needed
	if(drawTopBorder)
	{
		[topBorderColor set];
		[penPath moveToPoint: NSMakePoint(0, rect.size.height)];
		[penPath lineToPoint: NSMakePoint(rect.size.width, rect.size.height)];
	}
	
	//Draw Bottom Border if needed
	if(drawBottomBorder)
	{
		[bottomBorderColor set];
		[penPath moveToPoint: NSMakePoint(0, 0)];
		[penPath lineToPoint: NSMakePoint(rect.size.width, 0)];
	}
	
	[penPath stroke];
	[penPath release];
}


The Gradient drawing is done by Chad Weider's CTGradient code...I've even disabled this entirely thinking that it was the culprit...but still no luck.

I've tried [NSColor blackColor] and specifying RGB values of 0.0 and I cannot get a true black...always the .9333.

Anyone know what is going on?

----

Yes. You're using a CALIBRATED colour. That means it may change according to the current colorsync calibration to "look right" on your screen, etc. To get a mathematically pure black instead use     +colorWithDeviceRed:green:blue:alpha: instead. (Note: Apple recommends using calibrated colours in general). Also, your code falls into the classic trap of assuming that <rect> as passed to     drawRect: is your view's frame. It's not, it's the area needing update. So you will probably want to change that to use     [self bounds]. Finally, if you draw a 1 pixel line at the very top and bottom of the view, you'll find it is actually drawn only 0.5 pixels wide, because strokes are drawn along the centre of a path and the area outside of your view is clipped out. So you need to inset the positions of the start and end points by half of the stroke width. --GrahamCox

Followup: just noticed another bug/bad assumption. The colour isn't stored by the NSBezierPath object, so if the top and bottom colours are different, you'll only see one of them - whichever got set last before the call to     -stroke. If the colours are the same you can combine the paths into one, but if they are different then you'll have to create two paths and draw them separately. In this case you may find that NSBezierPath's class method     +strokeLineFromPoint:toPoint: might work better for you. --GC

----
Thanks for the info GC..  I read the rect issue on the common problems page after I pasted the code...I changed that and it still did not help.  I've also tried using device color also, and inset the line...and in my test case I'm only drawing the bottom line...and it's still comes out as .9333 for some reason.

The odd thing is that if I do:
    
[[NSColor blackColor] set];
NSRectFill([self bounds]);


Then the background is filled in completely in the correct color!! ?  Thanks for the strokeLineFromPoint:toPoint:, didn't know about that one :)

----

Maybe the colour is correct and what the colour meter is reporting is the antialiasing colours at the edge of the line. In fact, since you're drawing a 1 pixel line at an integer coordinate, I will bet that's exactly what's happening. So offsetting the line by 0.5 should cause the line to be drawn exactly on 1 pixel, and not straddling two, and it will be drawn in the correct colour. However since this is coincidentally exactly the same as what you need to do to make sure the line is wholly within the view and you say you've done that, it should now be right. Note that     +[NSColor blackColor] returns a calibrated colour. --GC

Another point: Neither Digital Color Meter nor Pixie report RGB colours in the notation you've given. In fact Pixie apparently doesn't tell you the colour at all. DCM reports CIE 1931 colours in this notation, but those values do not represent R, G and B. So maybe you're just confused? Does it LOOK black? --GC

----

Yeah...I've tried moving the line to the middle of the view, drawing it in 0 pixels (Doc says a value of 0 will draw the smallest support line), tried drawing 1 pixel, which as you have taught me, has drawn a 2 pixel line. :)

If you click on a pixel after you have locked the image in Pixie it reports RGB and then another set of numbers that I'm not entirely sure what they represent.  The RGB value it reports is in float format, and it reports correctly on the color if I do a rect fill.  Digital color meter reports 8 bit values if you select it, with some division you can get the float, still not 0.0.

It doesn't look black...it looks dark gray :)

Thanks for the help GC, I'll keep pluggin away at this to see if I can get the color to match.

----
GC you are a genious.  I started thinking about the antialiasing that you mentioned...so I moved the line to the middle of the view again and this time told it to draw a five pixel line...it worked, but the edges are antialiased of course, which is that color I was getting instead of black....  Is there a way to prevent NSBezierPath from drawing an antialiased line?

Thanks again.

----

GC, thank you sooooo much for the help.  I figured out I had to use     [[NSGraphicsContext currentContext] setShouldAntialias: NO]; to make it NOT draw the line AA.  Again, your a genius :), you gave me the clue I needed.  It works perfectly now.

Thank you, thank you, thank you, lol....  I fiddled with this for 4 hours last night trying to figure out how to draw a smiple line.  I came from a Microsoft .NET background and have only been doing Cocoa for about a week.  So you've been a great help.

----

See DrawRect for how to make your view not misbehave when partially invalidated.

