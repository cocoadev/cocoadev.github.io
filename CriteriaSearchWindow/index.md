---
layout: page
title: CriteriaSearchWindow
---

I'm trying to create a window like the Rules window in Mail or Search window in Finder, where clicking a + button adds a new criteria row. I've got as far as adding the new rows via addSubview but I can't make the new rows show up in the display. Does anybody have an implementation of this that I can review to see what I'm missing.

Here's the code I have that adds a new subview:

    
	archRow = [NSArchiver archivedDataWithRootObject:searchCriteriaView];
	NSRect bounds = [searchCriteriaSuperview bounds];
	NSView *row = (NSView *)[NSUnarchiver unarchiveObjectWithData:archRow];
	[row setFrameOrigin:NSMakePoint(bounds.origin.x, bounds.origin.y)];
	[searchCriteriaSuperview addSubview:row];


This works for the first subview but when I add a second one, I try to shift up the previous subviews:

    
	NSArray * subviews = [searchCriteriaSuperview subviews];
	for (c = 0; c < [subviews count]; ++c)
	{
		NSView * row = [subviews objectAtIndex:c];
		NSLog(@"row %d frame is at %d,%d, size %d by %d", c, (int)([row frame].origin.x), (int)([row frame].origin.y), (int)([row frame].size.width), (int)([row frame].size.height));
		NSPoint origin = [row frame].origin;
		[row setFrameOrigin:NSMakePoint(origin.x, origin.y + rowHeight)];
		[row setNeedsDisplay:YES];
	}


This doesn't work. I just get the one subview at the bottom of the superview window. What am I missing?

----

Are you shifting up the rows before or after adding a new row? If after, then it will shift up the newly added row as well. Also, don't forget to resize the superview and the window. -- RyanBates

----

Before I add the new row. And there's additional code that resizes the window. The superview is set in the NIB to auto-resize when the window resizes (it's an NSBox so I'm seeing it resize). The NSLog output seems to be reporting the right info (the positions of the rows before they are shifted) after I add two more rows:

    
2004-04-25 18:50:08.322 myapp[6210] row 0 frame is at 0,40, size 587 by 40
2004-04-25 18:50:08.323 myapp[6210] row 1 frame is at 0,0, size 587 by 40


----

Have you seen this? [http://www.stepwise.com/Articles/Technical/2003-12-20.01.html]

----

Yes, I had. But I think I now have a solution. Because I need to maintain an ordered array of views, I use arrayOfViews to hold the order of the criteria rows rather than [NSView subviews]. So to insert a criteria at postion index:

    
	if (index > [arrayOfViews count])
		index = [arrayOfViews count];
	for (c = 0; c < index; ++c)
	{
		NSView * row = [arrayOfViews objectAtIndex:c];
		NSPoint origin = [row frame].origin;
		[row setFrameOrigin:NSMakePoint(origin.x, origin.y + rowHeight)];
	}

	// Now add the new subview
	archRow = [NSArchiver archivedDataWithRootObject:searchCriteriaView];
	NSRect bounds = [searchCriteriaSuperview bounds];
	NSView *row = (NSView *)[NSUnarchiver unarchiveObjectWithData:archRow];
	[row setFrameOrigin:NSMakePoint(bounds.origin.x, bounds.origin.y + (((totalCriteria - 1) - index) * rowHeight))];
	[searchCriteriaSuperview addSubview:[row retain]];
	[arrayOfViews insertObject:row atIndex:index];

	[searchCriteriaSuperview display];
	[row release];


The only problem is that the view flickers visibly as the window is resized. Is there any way to reduce flicker as subviews are moved up and the window resized?

