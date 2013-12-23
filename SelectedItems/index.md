---
layout: page
title: SelectedItems
---

add this method as a category or subclass method of NSOutlineView

    
- (NSArray *)selectedItems
{
	NSIndexSet *selectedRowsIndexSet = [self selectedRowIndexes];
	int i;
	
	NSMutableArray *itemsArray = [NSMutableArray array];
	while ((i = [selectedRowsIndexSet indexGreaterThanIndex:i]) != NSNotFound) {
		[itemsArray addObject:[self itemAtRow:i]];
	}
	
	return itemsArray;
}

