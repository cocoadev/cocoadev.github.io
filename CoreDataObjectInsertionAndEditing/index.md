---
layout: page
title: CoreDataObjectInsertionAndEditing
---



[ Page re-factored to be a how-to. ]

Often times developers have the need to immediately edit an attribute (such as a title) of a newly-inserted object in a table. For instance, inserting a new "entry" into a list of entries, you'll want to immediately begin editing the title rather than naming it "New Entry" and forcing the user to double-click to edit the title, since that's the first thing users are likely to want to do. There are several key things to keep in mind when doing this with a CoreData application using the CocoaBindings mechanism.

----

**Insertion**

After the new entity instance is inserted into the managed object context (either via an object controller's     -add: method or by     NSEntityDescription's     +insertNewObjectForEntityForName:inManagedObjectContext: method), you should make any changes to the object, then call your managed object context's     -processPendingChanges method.

    
- (IBAction)newEntry:(id)sender
{
	// Add a new entry
	id newEntry = [NSEntityDescription 
                                insertNewObjectForEntityForName:@"Entry" 
                                inManagedObjectContext:[self managedObjectContext]];
       // Make changes to newEntry here ...

       // Force the context to process the changes
	self managedObjectContext] processPendingChanges];
	
       // Inform some controller that it needs
       // to rename the entry
	if (newEntry)
		[someController renameEntry:newEntry];
}


**Select & Edit New Object's Property**

The next step is to get the index of the newly-inserted object from the array controller, select it, then edit it. (The example below assumes     entryTitleColumn is of course a valid reference to our title column.) Since the array controller manages the table view's selection, you need to adjust the array controller's selection before attempting to edit the column/row or (due to timing issues), the request to edit will sporadically fail due to the array controller insisting on its own selection indices. That's the trick I was missing. :-)

    
- (void)renameEntry:(id)entry
{
       // Get the column index for our title column
	int col = [[entryTableView tableColumns] 
                         indexOfObject:entryTitleColumn];

       // Get the entry's index in the array controller's arranged objects
	int row = [[entryArrayController arrangedObjects] indexOfObject:entry];
	
       // Set the array controller's selection
	[entryArrayController setSelectionIndex:row];

       // Begin editing for the table view
	[entryTableView editColumn:col row:row withEvent:nil select:YES];
}


It's brain-dead simple once you know how ... Of course you could combine these two methods into one, but in my case, the editing was happening in a separate nib and the renaming code is to be reused, so I separated the two.

----

**Discussion**

*<Mike Myers jewish princess voice>[[NSArrayController - neitha' an array noa' a controlla' ... discuss.</Mike Myers jewish princess voice>*

FYI: Corrected code     int row = entryArrayController arrangedObjects] indexOfObject:entry]; (right?) --[[CharlesParnot

*Right. My bad. Thanks, again, Charles.*

----

Now how does this work with an NSTreeController and NSOutlineView? NSTreeController does not understand     setSelectionIndex: but rather requires an NSIndexPath via     setSelectionIndexPath:. How do you determine the index path to a given object as above?

