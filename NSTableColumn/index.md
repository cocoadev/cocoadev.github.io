---
layout: page
title: NSTableColumn
---

NSTableColumn is the class used by NSTableView to display a column of data cells, including the header cell at the top. Most of the stuff you would do with NSTableColumn has been referenced on other pages, mostly about NSTableView. *(If anyone can find these pages, please add them here)*

http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/Objc_classic/Classes/NSTableColumn.html
----


I am calling setBackgroundColor in dataCellForRow overide function in my subclass MyTableColumn as following:

    
/* MyTableColumn.m */

-(id)dataCellForRow:(int)row
{
    id theCell = [super dataCellForRow:row];
    if([theCell respondsToSelector:@selector(setBackgroundColor:)])
    {
        if(row%2)
            [theCell setBackgroundColor:[NSColor grayColor]];
        else
            [theCell setBackgroundColor:[NSColor whiteColor]];
    }
    return theCell;
}



But for some reason, the colors won't reflect on the tableview.  Can someone tell me what I am doing wrong here?  Thanks.

----

Check out:      - (void)tableView:(NSTableView *)aTableView willDisplayCell:(id)aCell forTableColumn:(NSTableColumn *)aTableColumn row:(int)rowIndex - That's the method you should be doing this in. ;-) Adding: Read the docs, this is a delegate method of NSTableView - this should go in the table's delegate and its - the table view's - delegate should be set.

