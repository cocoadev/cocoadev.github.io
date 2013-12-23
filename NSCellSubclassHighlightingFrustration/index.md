---
layout: page
title: NSCellSubclassHighlightingFrustration
---

I have created an NSTextFieldSubclass that I'm using to draw what is essential a bar graph representation of the cell's value in a table view. It works great - except for the row that is highlighted. If the row is highlighted, my bar graph completely dissapears.

Overriding (NSColor *)highlightColorWithFrame:(NSRect)cellFrame inView:(NSView *)controlView produces some interesting results, but nothing usable.

Has anyone encountered this before?

----

Probably need to see the code.  (Guess: you didn't implement copyWithZone:).

----
You mean to tell me you can't read my mind? :)

It is a fairly simple cell that takes the object value and uses that to calculate a perecentage which is used to draw a horizone bar graph.

If I only override drawInteriorWithFrame, it works great as long as the row isn't selected. This is what I'm using to draw the cell:

    

@implementation BarGraphCell

- (void) drawInteriorWithFrame: (NSRect)inFrame inView: (NSView*)inView
{
	NSRect theRect = NSMakeRect(inFrame.origin.x,inFrame.origin.y,inFrame.size.width,inFrame.size.height);
	theRect.size.width = theRect.size.width*(self objectValue] floatValue]);
	//theRect.size.height = 10;
	[[NSRectFill(theRect);
	[NSBezierPath strokeRect:theRect];
	
}



I'll be reading up on copyWithZone today to see if that solves my problem. For some reason though, I had thought that I didn't need to implement in this case. I'm sure I am wrong!

Thanks.

----

If you don't have any ivars, you don't have to implement copyWithZone:.

You aren't setting a color before you draw.  I'm guessing you wanted black, since that's probably what you (happened) to get in the usual case.  A few other minor bits you might want to be aware of inline:

    
- (void) drawInteriorWithFrame: (NSRect)inFrame inView: (NSView*)inView
{
	NSRect theRect = inFrame;  // no need to call NSMakeRect
	theRect.size.width *= [self floatValue]; // NSCell has a floatValue method
	[[NSColor blackColor] set]; // this is probably the important one
	NSRectFill(theRect); // stroke is probably not going to make it look better.  will probably fuzz the edges with the rect you used.
}


----

Ah, that worked perfectly - and more importantly, the reasons why make sense to me. Thanks a million!

