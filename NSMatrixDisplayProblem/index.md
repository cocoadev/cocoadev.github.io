---
layout: page
title: NSMatrixDisplayProblem
---

I'm working on an NSMatrix that is enclosed by an NSScrollView. The matrix will automatically reconfigure the number of colums to fit as many cells with a minimum width across the width of the scroll view. All of this works fine, but. When you ask the matrix to renew itself     renewRows:columns: when the number of rows and/or columns changes, the current selection will be cleared. If the matrix had multiple selections, these selections will be erased and the default list mode cell in the first row and first column (r,c {0, 0}) will be selected.

I do the following to preserve the mutiple selection:

    
    NSSize size = [NSScrollView contentSizeForFrameSize:[scrollView frame].size 
                                hasHorizontalScroller:NO
                                hasVerticalScroller:YES
                                borderType:[scrollView borderType]];

    // get selected cells before the matrix erases the selection with     renewRows:columns:
    NSArray *selectedCells = [matrix selectedCells];               

    // this custom method will renew row and column counts after calculating these 
    // values using the new scroll view's content size width
    [matrix recalculateNumberOfRowsAndColumsForVisibleWidth:size.width];     
    
    // reset the selected cells to be highlighted (I'm assuming that an NSMatrix uses this
    // cell parameter as the value that dictates the state of being selected)
    int count = [selectedCells count];
    while (count--) [[selectedCells objectAtIndex:count] setHighlighted:YES];
     


Anyways, the multiple selection is preserved, but when the matrix receives a     mouseDown: event that normally would deselect everything except the cell that is clicked on, the matrix does not redisplay all of the cells that were deselected (the unselected cells should not be highlighted anymore). I know that the state of these unselected cells are in fact unselected because if you drag over these cells in "list mode" the unselected cells will redisplay without being highlighted once the dynamic selection rectangle is no longer enclosing the unselected cell. 

Asking the matrix to dispay (    setNeedsDisplay:,     display and setNeedsDisplayInRect:) does not cause the unselected cells to redraw. Even asking the matrix to draw each unselected cell individually using     drawCellAtRow:column: does not cause the unselected cells to redraw. I realize that there must be some drawing optimization going on here, but I wish I could figure out what the rules are!!

