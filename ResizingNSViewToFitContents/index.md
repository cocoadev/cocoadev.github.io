---
layout: page
title: ResizingNSViewToFitContents
---

I have an NSView which contains a variable number of subviews. Is there any way to have the view resize to fit all of its contents?

----

Yes. Get the size of the contents and tell the view to resize to that size.

----

Not automatically.  Just write your own method.  Should be easy.

----

You could try something like this, but you are doing things backwards. Your subviews should be relative to your view not the other way around. 

    
- (void)fitSubviews {
	NSArray *subviews = [self subviews];
	NSRect rect = NSZeroRect;
	unsigned int i, count = [subviews count];
	for (i = 0; i < count; i++) {
		rect = NSUnionRect(rect, subviews objectAtIndex:i] frame]);
	}
	rect = [self convertRect:rect toView:[self superview;
	[self setFrame:rect];
}


Thanks, I got it. The reason I have to do it this way (I think) is because each of my subviews is the same size (I am doing a page-layout view), and every time a new page gets added, the parent view needs to get taller to encompass the new page.

----

If you want the window to resize smoothly (e.g. user preferences), use the     setFrame:display:animate: instead. -- BenoitMarchal

