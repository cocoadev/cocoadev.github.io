---
layout: page
title: SortDescriptorsWithNumericalData
---



This describes a document-based app using NSTableView to display floating point numerical data that can be positive or negative.
The app also uses NSArrayController and bindings to associate the model keys with columns in the table.
Now I want to add sort descriptors to the array controller so that the column sorting can be in algebraic order.

The keys in the original model class are strings, however. You can use caseInsensitiveCompare: as the selector to
sort the table in dictionary order, for example, when setting up a sort descriptor.

My naive attempt to set up algebraic sorting starts by creating a new model key accessor that takes the string
at the corresponding original key and returns an NSNumber containing a float. The setter accessor for the original key
releases the previous value of this NSNumber key and creates a new one from the string just set.
My reasoning is that I only need a getter accessor method in order to actually USE the NSNumber key for sorting.
Then I can add a sort descriptor to the array controller in the     awakeFromNib method of my window controller
to use the new NSNumber key when sorting the column bound to that key, as follows:

    
	NSSortDescriptor *c1sd = [ [ [ NSSortDescriptor alloc ] initWithKey: @"dataItem1n" ascending: YES
		selector: @selector( compare: ) ] autorelease ];
	...
	[ arrayController setSortDescriptors: [ NSArray arrayWithObjects: c1sd, nil ] ];


**The sort descriptor works perfectly at the time the data are loaded from a file** (rows are always numerical-ordered-ascending on that sort key at startup - tested by loading the same file saved with several row orderings.) However as soon as I click on the table column to re-sort the table (i.e, to toggle from ordered ascending to descending) the behavior reverts to ascii sorting based on the NSString keys.

By logging the array of sort descriptors from within the     arrangeObjects: method in the array controller, I can see that in     awakeFromNib there is one sort descriptor, as created above, using the key indicated. However, when I click the table column, that array suddenly contains **TWO** sort descriptors, one using the above key, and another using the original NSString key for that column, which sends it back to sorting in ascii order. Since the last-added descriptor determines the sort algorithm, I don't get the sorting I want. I tried removing all references to the original sort key from my array controller which is set up the standard way in IB, but it still grabs the string key as a sort key from the model class. I do not have the slightest clue how that second sort descriptor is being added to the array, since I am not doing it explicitly. This must be what goes on behind the scenes whenever you use the default sorting with bindings.

Now I recognize that the default behavior of the table header cell should be to reverse the sort order, except that the sort descriptor created above specifies to sort NSOrderedAscending. I would expect the table column click to crash the app, rather than automatically adding a sort key which I do not desire to an *existing* array of sort descriptors.

Perhaps if I allowed the sort descriptors to be instance variables in the window controller, I could modify the array of sort descriptors in a delegate method like didClickTableColumn: so that the reverse sort descriptor would be invoked when the column header is activated.

All the rest of my code treats the model keys without regard to their types, except where I transform my data to save to a text file or in creating a printable view. But the cells of the table view are NSTextCells - if I start to worry that I am getting bindings issues from that, I will just have to abandon the idea of algebraic sorting. If the provision of a special NSNumber key to sort the table is not sufficient, things seem a bit out of control. I only want to use them to sort the ARRAY of model objects - I don't want to get into using only NSNumber keys since the display of the model array in text cells in a table and in other views is already working like a charm.

With that, I think I have reached the limits of my ability to understand the details of bindings.

