---
layout: page
title: NSBrowserNoScrollingAndSelecting
---

How can I prevent an NSBrowser from both scrolling and changing the cell selection after reloading columns?

----

I haven't looked into it, but can't you retrieve the scroll position/selections and place them in variables? That way, after you reload it, just reset the scroll position and selection to the appropriate values. -- RyanBates

----

wouldn't that cause an extra re-draw? 
 I recall when I was having difficulty getting the browser to NOT change selection after I reloaded columns, it turned out that I was working with the browser improperly.  Instead of reloading, I should have been redrawing.  I forget the exact method to use, but it turned out to be a fairly easy fix once I realized that I was re-loading much too often.

----

Actually I did something like this in my code to restore the cell selections and scroll positions. Something like the following (I have not included the supporting functions like absoultePathOfRepositoryRoot, etc...):

    

- (IBAction) refreshBrowserContent:(id)sender
{
	NSString* pathRoot = [self absoultePathOfRepositoryRoot];
	NSArray* selectedPaths = [self absoultePathsOfBrowserSelectedFiles];

	// Save scroll positions of the columns
	int numberOfColumns = [theBrowser lastColumn];
	NSMutableArray* columnScrollPositions = [[NSMutableArray alloc] init];
	int i;
	for (i = 0; i <= numberOfColumns; i++)
	{
		NSMatrix* matrixForColumn = [fsBrowser matrixInColumn:i];
		NSScrollView* enclosingSV = [matrixForColumn enclosingScrollView];
		NSPoint currentScrollPosition = enclosingSV contentView] bounds].origin;
		[columnScrollPositions addObject:[[[NSValue valueWithPoint:currentScrollPosition]];
	}

	// Save the horizontal scroll position
	NSScrollView* horizontalSV = [fsBrowser matrixInColumn:0] enclosingScrollView] enclosingScrollView];
	[[NSPoint horizontalScrollPostion = horizontalSV contentView] bounds].origin;

	rootNodeInfo_ = ...compute this...;


	[self reloadData:self];
	
	// restore the selection and the scroll positions of the columns and the horizontal scroll
	[self restoreBrowserSelection:selectedPaths withColumnScrollPoisitions:columnScrollPositions andHorizontalScrollPosition:horizontalScrollPostion];
}


Then the restore is something like:

    
- (void) restoreBrowserSelection:([[NSArray*) selectedPaths withColumnScrollPoisitions:(NSArray*)columnScrollPositions  andHorizontalScrollPosition:(NSPoint)horizontalScrollPostion;
{
	if ([selectedPaths count] <1)
		return;

	// Restore the selection by looping over one of the selected paths and selecting the appropriate row in each column using
	... [theBrowser selectRow... ]...

	// Then finally selecting all the last path components and getting the rowIndexes in the last column and selecting them with:
	... [theBrowser selectRowIndexes:rowIndexes inColumn:(col-1)] ...

	// restore column scroll positions
	int i = 0;
	for (NSValue* position in columnScrollPositions)
	{
		NSPoint savedScrollPosition = [position pointValue];
		NSMatrix* matrixForColumn = [theBrowser matrixInColumn:i];
		NSScrollView* enclosingSV = [matrixForColumn enclosingScrollView];
		enclosingSV documentView] scrollPoint:savedScrollPosition];
		i++;
	}

	// restore horizontal scroll position
	[[NSScrollView* horizontalSV = [[[theBrowser matrixInColumn:0] enclosingScrollView] enclosingScrollView];
	[[horizontalSV documentView] scrollPoint:horizontalScrollPostion];
}

