---
layout: page
title: HowToChangeAlternatingRowColorInTableView
---



Hi all,

a simple question that an afternoon of Googling has not managed to finad an answer to:

NSTableView, since Panther, has had the option to automatically provide its rows with an 'alternating background' (blue, white, blue, white, etc.).

I can find no way to override the color for the alternating background however (I'd like to highlight the alternating rows in something other than blue and white). 

I am faced with a choice - find a way to get the 'alternating rows' feature to take a different color (not looking good based on my Googling so far), or go back to the Jaguar way of doing things and try and draw them myself... with all the pain that entails... (getting the highlights to extend all the way to the bottom... etc. etc. etc....)

If anyone could help me out, I'd be most grateful,

Cheers!

- Peter

----

The colors are supplied by NSColor's +controlAlternatingRowBackgroundColors (see the docs). If you don't mind this changing for all table views in your app (that use alt row colors), a category on NSColor would do.

----

That's great, thanks :)

----

For an alternate approach, I had to do some custom row background coloring recently and came up with a subclass of NSTableview.  This code simply replicates what the default NSTableView does.  Just change the color to whatever you'd like.

    

- (void)drawBackgroundInClipRect:(NSRect)clipRect;
{
	// make sure we do nothing so the drawRow method's drawing will take effect
}

- (void)drawRow:(int)row clipRect:(NSRect)rect;
{
	[[[NSColor controlAlternatingRowBackgroundColors] objectAtIndex:(row % 2)] setFill];
	NSRectFill([self rectOfRow:row]);
	
	[super drawRow:row clipRect:rect];
}


