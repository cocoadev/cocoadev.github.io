---
layout: page
title: ProgramaticallyCreateScrollViews
---



For a few of the applications I've been working on lately, I've been creating some views in code rather than IB, just because it makes a few things easier for me. I've had no problems EXCEPT for this one, which I've spent hours trying to solve. To make this easy to understand, take a look at this picture:

http://img65.imageshack.us/img65/2718/header4cz.jpg

Look at the bottom of the vertical scroller in this outline view. There seems to be a little bit of the document view clipped out. However, this is NOT the case-- if you drag the scroller, the document view stays the same even though the scroller moves. If you use the mouse scroll wheel, nothing happens. That little extra space just kind of appears for no reason that I can tell.

I've discovered a few things. First, it only happens when you tell an NSScrollView to use a documentView which has a -headerView method. In this case it's an NSOutlineView, but the exact same thing happens when I put in a custom NSView subclass with a headerView method.

Second, this only happens when there is no horizontal scroller. Put one in, and the scrollbar seems to take up the extra space which makes the problem go away. Calling -setHasHorizontalScroller on the scrollview does not fix the problem. Sadly enough, I don't want a horizontal scroller in my application.

Third, setting -setAutohidesScrollers:YES makes the problem go away. Again, this is almost a solution, but, well, I don't want to autohide the vertical scroller.

Last, this does not happen with an NSOutlineView I create in IB. I don't know why, I've tried just about everything to get the same results as the IB outline view, including subclassing it and setting a breakpoint so I could look at its private variables in the debugger, but I came away with nothing.

Here's some code, if it helps:

    
	NSView *contentView = self window] contentView];

	outlineView = [[[[SelectionOutlineView alloc] init];
	[outlineView setFocusRingType:NSFocusRingTypeNone];
	[outlineView setDataSource:[NSApp delegate]];
	
	NSScrollView *scrollView = [[[NSScrollView alloc] initWithFrame:outlineRect] autorelease];
	[scrollView setAutoresizingMask:NSViewHeightSizable];
	[scrollView setDocumentView:outlineView];
	[scrollView setHasVerticalScroller:YES];
	[scrollView setHasHorizontalScroller:NO];
	
	NSTableColumn *titleColumn = [[[NSTableColumn alloc] initWithIdentifier:GRFilterOutlineViewIdentifier] autorelease];
	titleColumn headerCell] setStringValue:@"Header"];
	[[titleColumn headerCell] setAlignment:[[NSCenterTextAlignment];
	[titleColumn setEditable:NO];
	
	[outlineView addTableColumn:titleColumn];
	[outlineView setOutlineTableColumn:titleColumn];
	[outlineView sizeLastColumnToFit];
	
	[contentView setAutoresizesSubviews:YES];
	[contentView addSubview:scrollView];


Anyway, if anyone could help me, even suggest a way I could further debug this, I would REALLY appreciate it. I don't think I could be the first one to run into this problem, but searching the mailing lists and google didn't give me anything. I've spent a lot of time on this, and I'm just about out of ideas-- anyone have any tips? 

*Is it just me, or do you never add anything to the scroll view in the code you posted?*

----
It's a bit hidden but that's up near the top-- [scrollView setDocumentView:outlineView].
----
Perhaps a well placed     tile would do the trick? Or maybe add the outlineView near the end, in case     tile isn't getting called as you're setting up the outlineView? I know it says "You rarely need to invoke this method" but maybe this is one of those rare cases? Shot in the dark, best guess, etc.
----
Sadly, calling -tile on either the NSScrollView or the NSBrowser does not fix the problem. I guess this would be the place where the scrollview is messing up-- when it lays out the components it's expecting to draw an extra scroller or part of the header view or something, even though it shouldn't. I suppose I could try subclassing NSScrollView and overriding -tile, but, well, I shouldn't have to.
----
Have you tried printing out the frames/bounds of the scroll view and all of its subviews for both the nib-created version and your manually-created version? I bet something's different. I often see strange offset frames for the documentView (with an origin at something like 16,16) for reasons unknown, and if you aren't duplicating that offset then that could be a problem.

On another note, since you've taken "hours" to fix this problem, why not just load a nib with the view already pre-constructed for you?
----
Thank you, I'm going to write a test to compare the bounds and frame now. Hopefully, I'll be able to track something down.

As far as not using IB, well, I shouldn't have to. There are times when creating interfaces with IB is a lot easier, especially when you're dealing with lots of widgets. For myself at least, there are plenty of other times when it's not easier, like when I'm laying out components in a splitview, or switching a window's content view between several views, and so on and so on. Maybe I'm just being a bit hardheaded when IB would certainly work, but it just feels like a bit of a hack to have to resort to using a nib when it's not the best solution.
----
When I'm in those situations, I just have a separate nib for each subcomponent, or even just a separate CustomView in the same nib. Then you can still get your easier layout and separation of stuff, and programmatic insertion of things, but without having to construct everything manually.
----
The way it's looking, I might just have to give trying to fix this and use IB. I played with my test program for a while, creating outline views in two windows of the same size, and comparing the resulting frame sizes. Here are the results.

    
IB OutlineView:
     ScrollView Frame: { {0, 0}, {480, 360} }
     DocumentView Frame: { {0, 0}, {463, 342} }
     ClipView Frame: { {1, 17}, {463, 342} }
         ClipView documentRect: { {0, 0}, {463, 342} }
         ClipView documentVisibleRect: { {0, 0}, {463, 342} }
     HeaderView Frame: { {0, 0}, {463, 17} }
     CornerView Frame: { {464, 0}, {16, 17} }
Coded OutlineView:
     ScrollView Frame: { {0, 0}, {480, 360} }
     DocumentView Frame: { {0, 0}, {465, 343} }
     ClipView Frame: { {0, 17}, {465, 343} }
         ClipView documentRect: { {0, 0}, {465, 343} }
         ClipView documentVisibleRect: { {0, 0}, {465, 343} }
     HeaderView Frame: { {0, 0}, {465, 17} }
     CornerView Frame: { {465, 0}, {16, 17} }


Although there are a few 1px differences in spacing, there are no major differences. They're not listed above, but the bounds as well as the scrollers both had the same results; things may be spaced 1 pixel differently, but mostly they turned out to be the same.

I also tried enumerating though all the subviews. One thing I noticed was that apparently the IB scrollview had an extra subview with an invalid frame, presumably for the empty horizontal scroller. However, when I instantiated a new NSScroller with an equivalent frame and set it as the horizontal scroller, it still didn't fix things. So, I just don't know what's wrong.

----
I think I may have just encountered a related problem due to the headerView.  I created a subclass of NSView as a container for 3 WebViews, which are created programmatically.  I could verify in the debugger that the WebViews were subviews of my view, but after the view was added to the view hierarchy of the window, one of the views was moved outside my view as a sibling.  I finally tracked the problem down to the fact that my view subclass had an instance variable named "headerView".  Evidently, NSScrollView has special, undocumented handling for a subview that has a headerView.  In my case, the first drawing of my class and subclasses was inconsistent after being added to the scroll view.

This may help explain the source of the problem, but the real question is how to deal with it.  I just renamed my instance variable.  In your case, I would suggest experimenting with building up your subviews differently.  In fact, I would try init'ing the outline view based on the content size of the scroll view:

    

	NSScrollView *scrollView = [[[NSScrollView alloc] initWithFrame:outlineRect] autorelease];
	[scrollView setAutoresizingMask:NSViewHeightSizable];
	[scrollView setHasVerticalScroller:YES];
	[scrollView setHasHorizontalScroller:NO];

	NSSize contentSize = [NSScrollView frameSizeForContentSize:outlineRect.size hasHorizontalScroller:NO hasVerticalScroller:YES borderType:???];

	outlineView = [[SelectionOutlineView alloc] initWithFrame:NSMakeRect(0, 0, contentSize.width, contentSize.height)];
	[outlineView setFocusRingType:NSFocusRingTypeNone];
	[outlineView setDataSource:[NSApp delegate]];

	[scrollView setDocumentView:outlineView];


