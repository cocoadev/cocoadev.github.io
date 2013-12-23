---
layout: page
title: SingleCellReloadData
---



Here is some code to make NSTableView only update one row, column, or cell at a time. These can be used instead of **-reloadData**, which updates the entire table.

This code is from the cocoa-dev mailing list, and was not written by me. �DustinVoss

----

    @interface NSTableView (SingleCellUpdateAdditions)

- (void) reloadDataForColumn:(int) x;
- (void) reloadDataForRow:(int) y;
- (void) reloadDataForColumn:(int) x row:(int) y;

@end

    @implementation NSTableView (SingleCellUpdateAdditions)

- (void) updateInRect:(NSRect) invalidRect
{
	if (!NSEqualRects(invalidRect, NSZeroRect))
	{
		[self setNeedsDisplayInRect: invalidRect];
	}
}

- (void) reloadDataForColumn:(int) x
{
	[self updateInRect: [self rectOfColumn: x]];
}

- (void) reloadDataForRow:(int) y
{
	[self updateInRect: [self rectOfRow: y]];
}

- (void) reloadDataForColumn:(int) x row:(int) y
{
	[self updateInRect: [self frameOfCellAtColumn: x row: y]];
}

@end

----

Doesn't **-reloadData** just update the visible rows? Dont get me wrong, I dont think that it is efficient to update the whole thing when only one row or cell needs to be updated. I just thought that it updated only the visible.

----

Aren't the row / column reload methods backwards? Shouldn't     reloadDataForRow call     rectOfRow and likewise for the columns?

Next time you could just fix it and let everyone know what you did. And now they aren't backwards. :-)

*I asked because I wasn't sure. I didn't test the code, and there could be some strange reason for it.*

----

Maybe a note should be added that this is a HACK! i.e. it relies upon the table view not caching cell content or bitmaps.

If you want better granularity in what's redrawn, you should instead switch to bindings!

