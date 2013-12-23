---
layout: page
title: NSMatrixResizing
---

How do I get the cells withing a NSMatrix to autoresize like a normal view?  I tried just setting the coil thing settings on an NSMatrix, but the cells widths don't adjust with the window.... **use the "autosize cells" option in NSMatrix's attributes panel**
----
Actually on a related line of thinking, is there a good clean way to get a NSMatrix to adjust the number of rows it has based on the width of the cells and how many will fit.  For example, if I have a matrix with 4 cells of fixed width, is there a nice way to automatically set the horizontal spacing to expand as the matrix expands, and when it squishes to an amount of space that will only fit 3 cells, to make a second row with the fourth item? (Imagine "Icon Cell View" like Interface Builder's instance tab item, although it should work with any kind of cell.. buttons etc.)
----

I realize that this isn't exactly what you were asking for, but it might get you started on something that leads to success. :-)

I use this class to display a resizable matrix of clickable thumbnail images in a simple image cataloging application. The ThumbnailMatrix lives inside an NSScrollView, which in turn lives in an NSSplitView.

--- ThumbnailMatrix.h ---
    
#import <Cocoa/Cocoa.h>

@interface ThumbnailMatrix : NSMatrix

- (id)initWithFrame: (NSRect)frameRect;
- (void)viewDidEndLiveResize;
- (void)update;

@end

--- ThumbnailMatrix.h ---

--- ThumbnailMatrix.m ---
    
@implementation ThumbnailMatrix

#define COLUMNS 6

- (id)initWithFrame: (NSRect)frameRect;
{
	NSAutoreleasePool * pool = [[NSAutoreleasePool alloc] init];
	float width = 0;

	[super initWithFrame: frameRect 
			mode: NSListModeMatrix 
		   prototype: [[[ImageActionCell alloc] init] autorelease]
		numberOfRows: 0 
	     numberOfColumns: COLUMNS];

	width = round(self superview] frame].size.width / (float)COLUMNS);
	[self setCellSize: [[NSMakeSize(width, width)];
	[self setIntercellSpacing: NSMakeSize(0, 0)];
	[self setAllowsEmptySelection: YES];
	[pool release];

	return self;
}

- (void)viewDidEndLiveResize;
{
	float width = round(self superview] frame].size.width / (float)COLUMNS);
	[[NSNotification * notification = [NSNotification notificationWithName: 
		@"thumbnailViewDidEndLiveResize" object: self];
	[[NSNotificationCenter defaultCenter] postNotification: notification];
	[self setCellSize: NSMakeSize(width, width)];
	[self sizeToCells];
	[self setNeedsDisplay];
}

- (void)update;
{
	float width = round(self superview] frame].size.width / (float)COLUMNS);
	[self setCellSize: [[NSMakeSize(width, width)];
	[self sizeToCells];
	[self setNeedsDisplay];
}

@end

--- ThumbnailMatrix.m ---

In the document that contains the matrix, you can add yourself as an observer for the "thumbnailViewDidEndLiveResize" notification:

    
	[[NSNotificationCenter defaultCenter] addObserver: self 
						 selector: (SEL)@selector(updateViews) 
						     name: @"thumbnailViewDidEndLiveResize" 
						   object: nil];


Then if you want to update any other views in your document after resizing, you can do so in your "updateViews" method.

--Tantle

