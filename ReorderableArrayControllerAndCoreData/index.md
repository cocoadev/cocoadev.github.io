---
layout: page
title: ReorderableArrayControllerAndCoreData
---

I'm having an interesting problem.  I have a basic CoreData app that uses an NSArrayController to manage some data in a table view.  I wanted to add reordering of the data, so I subclassed NSArrayController, and implemented some table view data source methods, hooked them up... and it sort of works.  When I drag reorder a row, I can see it appear where it should be for a second - then it's deleted, instantly!  I've tried this both with my code and a modified version of stuff on mmalcom's site.  At first I figured it was just a problem with my app, so I made a simple test project:

New CoreData app, add an entity called "Test", with a string attribute for "name."  Insert an array controller, an add button, a table view.  Hook them up.  Insert my drag reorder stuff, reorder, row appears there for a split second, then the object is removed from the store.  My code is below -- any leads would be very much appreciated.

    
#import "DragReorderingArrayController.h"

NSString *MovedRowsType = @"MOVED_ROWS_TYPE";

@implementation DragReorderingArrayController

- (void)awakeFromNib
{
    [tableView registerForDraggedTypes:
		[NSArray arrayWithObjects:MovedRowsType, nil]];
    [tableView setAllowsMultipleSelection:NO];
	[super awakeFromNib];
}

- (BOOL)tableView:(NSTableView *)tv
		writeRows:(NSArray*)rows
	 toPasteboard:(NSPasteboard*)pboard
{
    NSArray *typesArray = [NSArray arrayWithObject:MovedRowsType];
	
	[pboard declareTypes:typesArray owner:self];
    [pboard setPropertyList:rows forType:MovedRowsType];
	
    return YES;
}

- (NSDragOperation)tableView:(NSTableView*)tv
				validateDrop:(id <NSDraggingInfo>)info
				 proposedRow:(int)row
	   proposedDropOperation:(NSTableViewDropOperation)op
{
    
    NSDragOperation dragOp = ([info draggingSource] == tableView) ? NSDragOperationMove : NSDragOperationCopy;

    [tv setDropRow:row dropOperation:NSTableViewDropAbove];
	
    return dragOp;
}

- (BOOL)tableView:(NSTableView*)tv
	   acceptDrop:(id <NSDraggingInfo>)info
			  row:(int)row
	dropOperation:(NSTableViewDropOperation)op
{
    if (row < 0)
		row = 0;
    
    if ([info draggingSource] == tableView)
    {
		NSArray *rows = info draggingPasteboard] propertyListForType:[[MovedRowsType];
		int theRow = rows objectAtIndex:0]intValue];
		
		id object = [[[self arrangedObjects]objectAtIndex:theRow]retain];
		
		if (row != theRow+1 && row != theRow)
		{
			if (row < theRow)
				[self removeObjectAtArrangedObjectIndex:theRow];
			
			if (row <= [[self arrangedObjects] count])
				[self insertObject:object atArrangedObjectIndex:row];
			else
				[self addObject:object];
			
			if (row > theRow)
				[self removeObjectAtArrangedObjectIndex:theRow];
			
			[object release];
			
			return YES;
		}
		return NO;
    }
	
	return NO;
}

@end


----

Core Data uses [[NSSet and NSMutableSet, which do not support ordered indexing. There is no way to ensure data is stored or retrieved in order. The way around this is to add a property like -orderIndex to your entity and change *that* when rows are dragged. In other words, if #5 is moved to #3, everything from #3 and up is renumbered in the dragged order. Upon retrieval, you sort by the -orderIndex to maintain the dragged order.

----

Ah, well thanks for the tip.

