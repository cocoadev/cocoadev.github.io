---
layout: page
title: BestDesignApproachForDeletingTableRowsAndMatrixCells
---

A table view's data source is supposed to conform to the data source protocol. This protocol defines methods that a table view will need to retrieve and modify the data to display. The only thing that is not part of this protocol is a design approach for deleting and adding rows. How should the delete key event be handled. Should the table handle user requests to add rows? Should you subclass a table view and then add methods that the data source needs to respond to when the number of rows/cells are increased/decreased, or is there already support in AppKit to coordinate adding and deleting rows and cells between table views/matrices and their data source.

I guess an NSArrayController is the way to go. Putting off reading up on bindings was a mistake. 

----

Well, the NSArrayController handles all this for you if you're willing to go Panther-only.  If not, then you just need to have an action for adding and removing rows from your data source, and then call     -reloadData to make the table view reflect the changes.  For example, if your table view (called tableView below), for example, gets its data from an NSMutableArray (called tvArray below) which collects instances of MyCustomRowObject, then you're adding and deleting code (in your window controller or document class) would look something like this:
    
- (IBAction)addTVRow:(id)sender
{
	MyCustomRowObject* rowObj = [[MyCustomRowObject alloc] init];
	[tvArray addObject:rowObj];
	unsigned newObjIndex = [tvArray count] - 1;
	[tableView reloadData];
	[tableView selectRow:newObjIndex byExtendingSelection:NO];
	[tableView editColumn:0 row:newObjIndex withEvent:nil select:YES];
}
- (IBAction)deleteTVRow:(id)sender
{
	// I'll just do the single row variety here.
	// If you allow multiple row selection it's a bit more complicated
	int selectedRow = [tableView selectedRow];
	if (selectedRow != -1) {
		[tvArray removeObjectAtIndex:selectedRow];
		[tableView reloadData];
	}
}

And that's about it.  You then just set up your actions as the targets of the buttons or menu items you wish to invoke them, and you're there.  -- Bo

PS Matrices are a whole different ball of wax.  They don't use data sources, and they have built-in functions to add and delete rows that you can use.

