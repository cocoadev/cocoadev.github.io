---
layout: page
title: NSTreeControllerAndManagedObjectContext
---

I have a basic outline view and nstreecontroller connected which is propogated with two types of entities - groups and items.  Addition of items/groups and removal works great.  My problem arises with selections.  After deleting items from the view, the objects returned by the selectedObjects method correspond to the recently deleted objects, rather than the newly selected ones that arise from the removal.  Here's teh code that I'm using to delete from the managed context.  After this I want to process the selection by updating some other views.  

    
	if ([view numberOfSelectedRows] < 1)
		return;
	
	// check for editing row and end edit
	if ([view editedRow] >= 0) {
		NSTableColumn *tableColumn = view tableColumns] objectAtIndex:0];
		[[NSCell *dataCell = [tableColumn dataCellForRow:[view editedRow]];
		[dataCell endEditing:[view currentEditor]];
	}
	
    NSArray *selectedObjects = [controller selectedObjects]; 
    if ([selectedObjects count] > 0) {	
		NSManagedObjectContext *context = [dataSource managedObjectContext]; 
		NSEnumerator *objEnumerator = [selectedObjects objectEnumerator];
		id obj;
		while (obj = [objEnumerator nextObject]) {
			[context deleteObject:obj];
			[context processPendingChanges];
		}
	}


I tried adding a call to rearrangeObjects, but that was no help.  Is there a way to force the treecontroller to resync?  It's fine once I make a new selection.

Thanks.

