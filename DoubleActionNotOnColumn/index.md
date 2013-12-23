---
layout: page
title: DoubleActionNotOnColumn
---



I've got this associated as my doubleAction for my NSTableView:

    - (IBAction)doubleClickedTable:(id)sender
{
	if( 0 == [myTable numberOfSelectedRows] )
		[self addNewItemToTable:sender];
	
}

If the user double-clicks the blank space in the NSTableView, it creates a new item.  This is great.  However, when the user double-clicks a column to sort it, a new item is created, which is not something I want.  I tried doing **[myTable selectedRow]**, but this results in -1 whether the user clicks the column header or the blank space.  Does anyone have any ideas as to how I could get the action I'm looking for?  Thanks for the help.

----

*Why does the user need to double-click a column to sort the table?* Engineer your table so that selecting a column also sorts it. Of course you may only want uni-directional sorting of a column, and clickable-sortable columns toggle ascending and descending (at least by default with bindings). The delegate method didClickTableColumnHeaderCell may help (or whatever it is - my memory is shot by this hour of the day.)

Of course you may have that header cell click reserved for something else, but you haven't said so...

----

No, sorting a table column works exactly as it usually does.  My point was that you can double click the header of the column and it sorts it twice, once in one direction, than once in the other.  I basically need something kinda the opposite of what you said.... didNotClickTableColumnHeaderCell.... i need to check if the user double clicked the blank area in the table but NOT double clicked any header cell....  since [myTable selectedRow] is equal to -1 in both of these cases, is there any way to distinguish where the user clicked?

*Not sure if this will help, but try implementing the delegate method     tableView:mouseDownInHeaderOfTableColumn: and see if it is called before or after the double-action (or if it is not called at all). --JediKnil*

----

Check     if([tableView clickedRow] != -1) before you create the new item.

