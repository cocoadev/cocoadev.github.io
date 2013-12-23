---
layout: page
title: HowToDisableRowContentsInNSTableView
---



I am trying to make certain rows in NSTableView to be disabled.  I have tried to use setEnabled method, which didn't work for me.  Can someone help me to solve this problem?  Thanks

----

What do you mean disabled? Do you have a text cell that you don't want to be editable or do you have  controls embedded in the rows you want disabled? Check out the delegate methods in the documentation for NSTableView     - (BOOL)tableView:(NSTableView *)aTableView shouldEditTableColumn:(NSTableColumn *)aTableColumn row:(int)rowIndex is what  you're most likely looking for.

    
// This belongs in your tableview's delegate. It looks for the cell at disabledRow and textColumn (made up - 
// assume these are valid. You're free to determine whether the cell should be editable any way you like, though.
- (BOOL)tableView:(NSTableView *)aTableView shouldEditTableColumn:(NSTableColumn *)aTableColumn row:(int)rowIndex
{
     if (rowIndex == disabledRow && aTableColumn == textColumn)
     {
          // I don't want the cell  at disabledRow / textColumn to be editable
          return NO;
     }

     // Assume yes, otherwise
     return YES;
}

----
And if you want a text cell to appear disabled..
    
- (void)tableView:(NSTableView *)aTableView willDisplayCell:(id)aCell forTableColumn:(NSTableColumn *)aTableColumn row:(int)rowIndex
{
     if ([aCell type] == NSTextCellType)
          [aCell setTextColor:[NSColor disabledControlTextColor]];
}


----
By disabled, I mean that I want to display the text cell in gray, but I don't want it to have any response to the mouse, such as edit, selet or drag.  I think this will help, thanks a lot.

----

In that case, you will want to tell the cell itself     [aCell setEnabled:NO];. One approach is to adapt the second example above. I use that method for exactly this, though there are probably better ways to do it.

*    [aCell setEnabled:NO]; doesn't work. The OP (logically) tried that first, as did I (to confirm). The simple solution is to use these delegate methods (and return NO from     tableView:writeRows:toPasteboard: for those disabled rows). There are other ways to do it but probably not better ways - this is all pretty simple and doesn't require any dirty tricks or subclassing.*

**In the case of the text cell, setEnabled:NO and setEditable:NO are used. This will give the appropriate effect.**

*No, it won't produce the appropriate effect. setEnabled:NO fails to draw in the disabledControlTextColor but disallows editing like it's supposed to (meaning it acts the same as setEditable:NO) when the text cell is in an NSTableView. Also, you don't call setEnabled:NO followed by setEditable:NO because a disabled cell can't be edited anyway.*

