---
layout: page
title: NSButtonCellInBoundColumn
---



I have an NSArrayController that contains Foo objects. Each Foo object contains (among other things) an array of Bar objects called "bars". I have an NSTableView for my Foos, whose only column is bound to the Foo array controller. With me so far? ;-)

I have another NSArrayController for the Bar objects whose content is bound to Foo's "selection.bars".  The selected Bars table has two columns: "Bar Title" and "X" (for delete bar from selected foo). In the "X" column, I have set the data cell type to NSButtonCell to get a pretty little round 'x' button to remove the Bar from the selected Foo when clicked. To accomplish this, the "X" column's Target is bound to Foo's "selection" with "removeBar:" as the selector. Argument is bound to Bar's "selection".

Now, all works well when I click the 'x' button next to a Bar that's currently selected. However, if I click the 'x' button next to a Bar that's *not* selected, only the currently-selected Bar is removed from Foo.

So how do I go about this? It can't be *that* hard to make it work with Bindings, can it? Or is this one of those situations where bindings doesn't make as much sense as just using data sources?

----

If you're trying to bind to specific properties of the data cell, there does seem to be a limitation. Since the dataCell is only one prototype cell, its own properties evidently can't be bound per-row - at least not in Interface Builder. The solution is rather convoluted but works just fine (performance not really put to the test yet ...). I found the post on the Cocoa-Dev list when I was trying to solve the same kind of problem just recently: http://haoli.dnsalias.com/forums/viewtopic.php?t=60048

To summarize, you need to catch the tableView:willDisplayCell:forTableColumn:row: delegate and manually bind each cell. DO NOT BIND ANYTHING in IB. Just set the data cell type. From the code below, you can hook up to any of the data cell's exposed bindings. Someone correct me / refine this statement if I'm wrong, please.

    
- (void) tableView: (NSTableView*) tableView willDisplayCell: (id) cell 
forTableColumn: (NSTableColumn*) tableColumn row: (int) row 
{ 
    if (tableColumn identifier] isEqual: @"operator"]) { 
        [[CBFilter* filter = filtersController arrangedObjects] objectAtIndex: row]; 
        [cell bind: @"selectedValue" toObject: filter withKeyPath: @"value" options: nil];
    } 
} 


Obviously, this will require some understanding of [[CocoaBindings and some tailoring to suit your needs, but if you want to also modify a property that only the data cell has (like an NSButtonCell's alt image), you obtain the [tableColumn dataCell] and set it directly or bind it to something else to get a dynamic button, for instance. Again, with a little understanding, table data source and Bindings methods can actually be pretty powerful together.

- JNozzi

----

**Updated 7/2005:** Following is the method I use to accomplish this same task. Rather than bind anything at all, I make the model object (in this case, 'Task' objects) the target. By putting a     -openSelfInTaskEditor method in my Task object, I can make the task responsible for requesting itself to be open. This makes for a 'cleaner' solution in my mind. The cell (in this case a pretty little magnifying glass button) targets the task at the appropriate row. This works seamlessly with bindings (as we're not really getting or setting values, we don't have to worry about 'going behind bindings' back'). Remember to set the button cell column's identifier in Interface Builder (or in code if you prefer) - that's how it knows to connect the buttons. Also remember that tableView:willDisplayCell:forTableColumn:row: is a *delegate* method, so the table view's delegate outlet must be connected to the object containing the method below.

    
- (void)tableView:(NSTableView *)aTableView willDisplayCell:(id)aCell forTableColumn:(NSTableColumn *)aTableColumn row:(int)rowIndex
{
       NSString * identifier = [aTableColumn identifier];

	// Tasks Table
	if (aTableView == tableTasks)
	{
		// Inspector button column (opens task for detailed editor view)
		if ([identifier isEqualToString:@"inspect"])
		{
			id task = acTasks arrangedObjects] objectAtIndex:rowIndex];
			[aCell setTarget:task];
			[aCell setAction:@selector(openSelfInTaskEditor)];
		}
	}
}


    tableTasks is of course the table with bound columns.
    acTasks is an [[NSArrayController in charge of the task objects.

----

I'm having problems getting the same thing to work, I'm catching the tableView:willDisplayCell:forTableColumn:row: deletage method but am unsure what to bind or set in cell for it to work.

I want the NSArrayController to remove the row of the button that was clicked rather than have a seperate delete/remove button.

----

I've got a similar problem - I  have an array of dictionaries, of which one of the keys I use for a cell checkbox in an tableview. I have added an arraycontroller for the array, I've bound the column containing the checkbox cells to the array controller's "arrangedObjects" with a keypath of the appropriate dictionary key ('checked', an NSNumber object in this case). When the data is displayed in the tableview, the checkbox states are properly set from the data; however, when I click on a checkbox to check/uncheck it, the actual value in the array's dictionary object is not being set.

I've tried to set the actual cell's binding to the array controller's selection object with the same key path also/instead, but this hasn't worked either.

Is this a limitation of how cells work, as described above? Would I need to implement the workarounds above to achieve what I want (I've attempted it but not sure if I got things right)?

 ----

Does one have to provide a value transformer in such a case?

