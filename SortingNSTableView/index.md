---
layout: page
title: SortingNSTableView
---



So, as I said, I've got an NSTableView with an array of NSDictionary objects.  i want to create a sort function, but as shown on SortingExampleUsingNSComparisonResult, you need the sort function inside of the declaration of the object.  can i put a sortItems: function in the NSDictionary definition, or somehow tell it to use a function from my Controller file?

In 10.3, there is NSSortDescriptor which handles this job almost automatically. Sort descriptors are objects telling an array full of dictionaries how to sort itself.

    
NSArray *arrayOfDictionaries; // Your dictionary to sort
NSSortDescriptor *sortDescriptor = [[NSSortDescriptor alloc] initWithKey:@"Dictionary key" ascending:YES];

NSArray *sortedArray = [arrayOfDictionaries sortUsingDescriptors:[NSArray arrayWithObject:sortDescriptor]];
[sortDescriptor release];


As you can see, you don't have to write your own routine to sort the array by a key common to all the dictionaries. The only thing needed is that the objects specified by the dicionary key (@"Dictionary key") has to respond to compare: . Also note that you can pass the array an array of sort descriptors, to specify secondary, tertiary sorting.. HTH EnglaBenny

More: even better, I forgot that you can specify sort descriptors in Interface Builder! Having done that, sorting is really easy:

    
- (void)tableView:(NSTableView *)tableView didClickTableColumn:(NSTableColumn *)tableColumn {
	// Sort the table
	NSSortDescriptor *sortDesc = [tableColumn sortDescriptorPrototype];
	if(sortDesc == nil)
		return;
	NSArray *sortDescriptors = [NSArray arrayWithObject:sortDesc];
	[dropContentsArray sortUsingDescriptors:sortDescriptors];
	[self setHasChanged:YES];
}


----

okay, great, I did the second option, using IB and the column clicking.  however, there is an option in IB for Ascending or Descending.  i want it to switch back and forth between the two when clicking the column.  IB already has this, kinda, with the Indicator Image switching back and forth between up and down arrows.  but is there anyway to get the sort to change?  this was my idea:

    
- (void)tableView:(NSTableView *)tableView didClickTableColumn:(NSTableColumn *)tableColumn {
	
	NSSortDescriptor *tempDesc = [tableColumn sortDescriptorPrototype];
	NSSortDescriptor *sortDesc = [NSSortDescriptor alloc];
	if(sortDesc == nil)
		return;

	if( [tableView indicatorImageInTableColumn:tableColumn] == [NSImage imageNamed:@"NSDescendingSortIndicator"] ) {
		sortDesc = [NSSortDescriptor initWithKey:[tempDesc key] ascending:YES];
		[tableView setIndicatorImage:[NSImage imageNamed:@"NSAscendingSortIndicator"] inTableColumn:tableColumn];
	}
	else if( [tableView indicatorImageInTableColumn:tableColumn] == [NSImage imageNamed:@"NSAscendingSortIndicator"] ) {
		sortDesc = [NSSortDescriptor initWithKey:[tempDesc key] ascending:NO];
		[tableView setIndicatorImage:[NSImage imageNamed:@"NSDescendingSortIndicator"] inTableColumn:tableColumn];
	}

	NSArray *sortDescriptors = [NSArray arrayWithObject:sortDesc];
	[dropContentsArray sortUsingDescriptors:sortDescriptors];
	[tableView reloadData];

}


so basically, the sort descriptor in the IB table is copied, and the appropriate ascending/descending direction is applied, based on the image that is in the table column.  this, however, doesn't work.  does anyone else have any ideas?

The image comparisons certainly won't work; [NSImage imageNamed:@"NSDescendingSortIndicator"] probably creates a new instance that won't have the same address as the indicator image... Try saving the sorting direction in your data source. Also note that NSSortDescriptor has a method named -[NSSortDescriptor reversedSortDescriptor] EnglaBenny

----

I think that's what the following table view dataSource method is for:

    

- (void)tableView:(NSTableView *)aTableView sortDescriptorsDidChange:(NSArray *)oldDescriptors;



So, basically, you get rid of the tableView:didClickTableColumn: method and use the tableView:sortDescriptorsDidChange: method instead. When the app is running, and you click on one of the column headers, it has the effect of changing the sort descriptors. So, you could use the following method to give sorting functionality:

    

- (void)tableView:(NSTableView *)tableView sortDescriptorsDidChange:(NSArray *)oldDescriptors {
	NSArray *newDescriptors = [tableView sortDescriptors];
	[myDataSourceArray sortUsingDescriptors:newDescriptors];
	[tableView reloadData];
}



This seems to handle setting the correct image (up or down arrow) and highlighting the correct column all automatically. -- Mark Douma

