---
layout: page
title: OneTimeEditInTableView
---


----

NSTableView has some slightly odd choices for its default behaviour. One of them is that if you edit a text item, then hit return (or tab, etc.) to end that edit, after calling the delegate's     tableView:setObjectValue:forTableColumn:row: it looks for another cell to edit, and selects it for editing. This is all well and good but many times isn't appropriate - if the table is kept sorted on the string you edited for example, it should probably reload and change the selection to the same item with its new name. The only way I found to implement a "one time" edit for an item like this was yet again to override the     textDidEndEditing: method in the table view itself, like so:

    

- (void)		textDidEndEditing:(NSNotification*) aNotification
{
	// this overrides the standard behaviour so that ending text editing does not select a new cell for editing
	// Instead the delegate is called as normal but then the table is made 1stR.
	
	NSTableColumn* theColumn = self tableColumns] objectAtIndex:[self editedColumn;
	
       self delegate] tableView:self setObjectValue:[[aNotification object] string] forTableColumn:theColumn row:[self selectedRow;
	self window] makeFirstResponder:self];
}




It's still up to the delegate to reload the table and change the selection to whatever is appropriate, but this method override ensures that it can do so effectively, without the default "select another cell" behaviour getting in its way. Hope this is useful to someone. --[[GrahamCox

