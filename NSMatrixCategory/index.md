---
layout: page
title: NSMatrixCategory
---



Category for NSMatrix that allows moving rows and columns. Feel free to add anything else you find useful. Also check out DragMatrix, a very useful subclass of NSMatrix that can quickly be modified to accommodate dragging other types of cells (besides images).

Code for **NSMatrix+CocoaDevUsersAdditions.h**
    
 #import <Cocoa/Cocoa.h>
 
 @interface NSMatrix (CocoaDevUsersAdditions)
 - (void)moveColumn:(int)srcCol toColumn:(int)dstCol;
 - (void)moveRow:(int)srcRow toRow:(int)dstRow;
 @end


Code for **NSMatrix+CocoaDevUsersAdditions.m**
    
 #import "NSMatrix+CocoaDevUsersAdditions.h"
 
 @implementation NSMatrix (CocoaDevUsersAdditions)
 - (void)moveColumn:(int)srcCol toColumn:(int)dstCol
 {
   if (srcCol == dstCol) return;
   
   int row = [self numberOfRows];
   NSMutableArray *cellsInCol = NSMutableArray alloc] initWithCapacity:row];
   
   for (row--; row >= 0; row--) {
     [cellsInCol insertObject:[self cellAtRow:row column:srcCol] atIndex:0];
   }
   
   [self removeColumn:srcCol];
   [self insertColumn:dstCol withCells:cellsInCol];
   
   [cellsInCol release];
 }
 
 - (void)moveRow:(int)srcRow toRow:(int)dstRow
 {
   if (srcRow == dstRow) return;
   
   int col = [self numberOfColumns];
   NSMutableArray *cellsInRow = [[NSMutableArray alloc] initWithCapacity:col];
   
   for (col--; col >= 0; col--) {
     [cellsInRow insertObject:[self cellAtRow:srcRow column:col] atIndex:0];
   }
   
   [self removeRow:srcRow];
   [self insertRow:dstRow withCells:cellsInRow];
   
   [cellsInRow release];
 }
 @end

----

*Why make a copy of cellsInRow? Why not just pass in cellsInRow to     -insertRow:withCells: ?*

I think that immutable arrays don't carry much of the "baggage" that mutable arrays do (e.g., current capacity, resizing capability). But I could be wrong. There won't be any functional change if you don't make a copy -- I just thought it might help.

*I think at best it will add a small overhead. I've changed the code :)*


[[Category:CocoaDevUsersAdditions

