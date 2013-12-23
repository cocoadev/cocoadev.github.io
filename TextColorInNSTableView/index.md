---
layout: page
title: TextColorInNSTableView
---



So I've implemented the     - (void)tableView:(General/NSTableView *)aTableView willDisplayCell:(id)aCell forTableColumn:(General/NSTableColumn *)aTableColumn row:(int)rowIndex delegate method to change the text color of the General/NSTableView's cells to red.  However, this delegate method seems to only be called for one of my 4 columns, not all of them.  As a result, only one column of my table is red, while the rest remain black.  Why is this?  Thanks for the help.

----

what does your     willDisplayCell:... look like? are you doing any testing on the the General/NSTableColumn that's passed in?

----

well, here is one test that i did:

    - (void)tableView:(General/NSTableView *)aTableView
  willDisplayCell:(id)aCell
   forTableColumn:(General/NSTableColumn *)aTableColumn
			  row:(int)rowIndex
{
	General/NSTextFieldCell *temp = General/[[NSTextFieldCell alloc] init];
		
	if( [aCell class] == [temp class] )
		General/NSLog( @"this is a text field cell" );

	[aCell setTextColor:General/[NSColor redColor]];

}

and when i ran it, the "this is a text field cell" string only appeared in my General/NSLog 8 times, the number of rows i had, not 8 * 4, the rows times the columns, or the total number of cells.  also, only the very first column had the red text, not all 4 columns.  so I'm not sure whats up.

----

are you supplying data for all columns in your     objectValueForTableColumn:...? are all the columns cells General/NSTextFieldCells?      if( [aCell isKindOfClass:General/[NSTextFieldCell class]] ) is probably more reliable than testing on equality.
