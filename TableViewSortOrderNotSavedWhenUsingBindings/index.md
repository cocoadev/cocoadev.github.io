---
layout: page
title: TableViewSortOrderNotSavedWhenUsingBindings
---



I have an odd problem.  I have created a table of URL bookmarks.  Using bindings, I can add and remove bookmarks without any trouble.  I can also save them.
The problem starts when I sort.  For some reason, the sort order is not reflected in the underlying array of bookmarks.  So while the table looks like it is sorted,
the underlying array is not.  Thus if I save that data array, the sort order is lost, and next time I start the application, everything is back to the unorted way.
Is there any way I can force bindings to synchronize the GUI view with the underlying data array?

Some more details:

The table view is bound to General/NSArrayController, with controller key arrangedObjects, and model key path General/URLOfBookmark
General/NSArrayController's contentArray is bound to File's Owner with model key path bookmarksArray

I have tried to use 
    
- (void)tableView:(General/NSTableView *)tableView sortDescriptorsDidChange:(General/NSArray *)oldDescriptors
{
	[bookmarksArray sortUsingDescriptors:[bookmarksController sortDescriptors]];
}

While that sorts the array, the controller does not expect this, so if I do any subsequent additions and deletions, the wrong entries are removed.

----

For some reason this is not being posted.  In the meantime, I read a posting on the Apple List by someone who had a similar problem.  By saving
[bookmarksController arrangedObjects] rather than bookmarksArray, the order was preserved
