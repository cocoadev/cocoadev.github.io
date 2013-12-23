---
layout: page
title: SortingThenEditingNSTableViewChangesWrongRow
---



I'm working on a bug-tracking application using an NSTableView. When I click on a column header to change the sorting order, and then change an entry in one of the rows, the change shows up in the wrong row. I'm using key-value observing, and here's observing code that makes the changes so they're undoable. 


    
- (void) changeKeyPath:(NSString *)keyPath
			  ofObject:(id)obj
			   toValue:(id)newValue 
{
	[obj setValue:newValue forKeyPath:keyPath];
}

- (void) observeValueForKeyPath:(NSString *)keyPath
					   ofObject:(id)object
						 change:(NSDictionary *)change
						context:(void *)context
{
	if (object==bugController) { // trying to find the selected row
		unsigned int sel = [bugController selectionIndex];
		if (sel == NSNotFound) {
			NSLog(@"No row selected\n");
		} else {
			NSLog(@"Selected row is %d\n", sel);
		}
	} else {
		NSLog(@"Observing for undo manager\n");
		// unsigned int sel = [bugController selectionIndex];
		NSArray *theseBugs = [bugController selectedObjects];
		Bug *selectedBug = [theseBugs objectAtIndex:0]; // always only one object is selected
		if (selectedBug != object) {
			NSLog(@"Changing wrong bug\n");
		}
		NSUndoManager *undo = [self undoManager];
		id oldValue = [change objectForKey:NSKeyValueChangeOldKey];
		undo prepareWithInvocationTarget:self] changeKeyPath:keyPath
													  ofObject:object
													   toValue:oldValue];
		[undo setActionName:@"Edit"];
	}
}


The code above can correctly determine that it's about to change the wrong row, but I haven't figured out why the error is occurring, or a good way to solve it. I've read through the documentation on [[NSTableView and NSArrayController without much luck so far. 

Has anyone had experience with this kind of error, or have any suggestions on what I should look for when debugging?

Thanks!

----

I think I know why it's happening, though off the top of my head don't have a definitive solution. It might give you something to go on though. When the row is edited it will call the datasource to change the value. When that gets changed the list will get resorted (does it? not clear from the code) before the call stack returns back to the table view. The table view caches the row index being edited which is now incorrect, and therefore will update the wrong row. Maybe you can reorder things so that the sorting is delayed until the edit is fully completed, or perhaps try reloading the whole table when an edit completes so its internal state is rebuilt. I just had some similar issues the other day with a table view and ended up needing to subclass it (with only some minor overrides) to get it to behave the way I wanted. The default behaviour is OK for many situations but not all - I think if editing a row implies that its index should change (in my case this was true and seems like it's yours too) it's one of those cases where the default behaviour gets in the way. --GrahamCox

----

I finally solved the problem by taking a different approach, which I found at http://developer.apple.com/documentation/Cocoa/Conceptual/CocoaBindings/CocoaBindings.html

Instead of adding an NSComboBoxCell programatically like I had been doing, I grabbed an NSPopUpButtonCell from IB's Cocoa-data palette, applied it to the relevant column, and set the bindings for the column according to Apple's documentation. You can use value transformers if you want.

