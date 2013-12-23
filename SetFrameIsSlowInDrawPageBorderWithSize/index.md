---
layout: page
title: SetFrameIsSlowInDrawPageBorderWithSize
---



Hi everybody. I've got a problem. I'm printing a subclass of NSTextView and in subclass I use this method:
    
-(void)drawPageBorderWithSize:(NSSize)borderSize
{
	NSRect savedTextRect = [self frame];
	[self setFrame:NSMakeRect(0, 0, borderSize.width, borderSize.height)];
	[self lockFocus];
	
	...
	// some drawing around text here
	...

	[self unlockFocus];
	[self setFrame:savedTextRect];
}

This method is called once for each page. The problem is that the more pages you print at once the slower it gets. So if you print 50 pages, the first page takes only some ms, but the last one takes already more than 5 seconds(!). It's really slow (G4 450). Shark told me that 80% of time is spent in both     setFrame: messages.
So just to check if I'm correct, I commented those methods, and everything was printed instantly, but of course then it doesn't look as I want it to.
I need to change the frame so I can draw things around text. I'm stalled. I just don't know how to speed up it or how to workaround it.

----

Um, well, it's most likely the "// some drawing around text here" code... so please post it.

*Instead of setting the frame, why not just draw the border on the existing frame? Not only that, but the frame is the location and size of the actual view **in the window.** Maybe you should try setting the bounds instead. This won't change the actual size/shape of the view, but it will probably allow you to add the border without drawing on top of the text.*
----
I'm the original poster. I'm sure it's not my drawing methods, I tried to not draw anything at all, but the problem persist.
In Shark I see     setFrame: itself calls     NSLayoutManager glyphRangeForBoundingRect:inTextContainer:, and it takes the most time out of my printing process. I believe when I change page frame then LayoutManager tries to calculate layout of glyphs for this page starting from the first one, so if I'm at the page 50, it has to recalculate all previous 49 pages layout. Something like that I think. And that is why it prints instantly if I don't change the frame - LayoutManager (or whatever is responsible) just knows that all previously calculated layouts are still valid, so it just need to layout only current page. 

To the question why I change frame. I have an offscreen view with its frame set for the area where text will be. So to draw borders I change its frame to include margins so I can draw there. This is standard procedure described here (at the bottom of the page): http://developer.apple.com/documentation/Cocoa/Conceptual/Printing/Tasks/PaginatingViews.html

The Smultron use exactly the same method, and it has the same problem - try to print relatively large text (50 pages), and you'll see what I'm talking about.
I believe all programms who use that method to draw headers or borders around text, have the same problem. So I would be happy to escape this solution. I tried to change bounds, but that doesn't work - nothing is drawn, and text gets clipped.

*(You don't need to make every concept a wikilink ;)*

----

I think you should be using NSTextContainers for page layout. --zootbobbalu

