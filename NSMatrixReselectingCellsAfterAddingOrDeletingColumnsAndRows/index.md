---
layout: page
title: NSMatrixReselectingCellsAfterAddingOrDeletingColumnsAndRows
---



Here's sample code that shows how to reselect cells after rows or columns have been added/removed from a matrix. This issue pops up when you enclose a matrix in a scroll view and then you would like the matrix's width to fit the content width of the scroll view. If you resize the matrix while also enforcing a minimum cell width, you will have to add and delete rows and columns accordingly. If you would like the state of each cell to persist after rows/columns have been added/removed, you have to record the indicies of the selected cells before the matrix's row/column count is altered and then reselect the cells after rows/columns have been added/removed.

This sample code is for a custom matrix method that adds/removes rows/columns when its enclosing scroll view's content size changes.

    
- (void)resizeMatrix {
	NSArray *selectedCells = [self selectedCells];
	NSMutableIndexSet *indexSet = [[[NSMutableIndexSet alloc] init] autorelease];
	unsigned int i, selectedCellCount = [selectedCells count];
	NSArray *cells = [self cells];
	for (i = 0; i < selectedCellCount; i++) {
		[indexSet addIndex:[cells indexOfObject:[selectedCells objectAtIndex:i]]];
	}
	selectedCellCount = [indexSet count];

        /*
        
               add/remove rows/columns here

        */

	[self deselectAllCells];
	i = [indexSet firstIndex]; 
	do [self setSelectionFrom:i to:i anchor:i highlight:YES];
	while ((i = [indexSet indexGreaterThanIndex:i]) != NSNotFound);


}


--zootbobbalu

