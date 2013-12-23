---
layout: page
title: NSTableColumnPreventDrag
---



Is there a way to prevent certain columns in a NSTableView from being dragged around, and only allow other columns? I see that there is a delegate method     tableView:didDragTableColumn: but I need something like     tableView:shouldDragTableColumn:. I also need the table column to be sorted, so I can't override     tableView:shouldSelectTableColumn: to return NO.

----

Without looking I can't say whether or not there's a built-in mechanism for this. I'm not going to look because you can. ;-)  However, if there are none, you might try subclassing NSTableColumn and giving it a -(BOOL)allowsDrag and a -(void)setAllowsDrag: method. Override the beginDrag or related method and if -allowsDrag is NO ... don't. At runtime, you can copy all the properties you set up in IB to your custom column object, then swap them out into the table. Or, you could create all columns programmatically, which would probably be better performance-wise, though I can't imagine it being too big a difference.

----

From the NSTableView page:

    - (BOOL)canDragRowsWithIndexes:(NSIndexSet *)rowIndexes atPoint:(NSPoint)mouseDownPoint

Returns whether the receiver allows dragging the rows at rowIndexes with a drag initiated at mousedDownPoint. Return NO to disallow the drag.

**Availability**

Available in Mac OS X v10.4 and later.

That in the right ballpark?

----

Thanks, but that's for rows, not columns, and only for 10.4. Since I need it for 10.2.8 and up, I guess I'll just have to do my own custom stuff... thanks anyways.

----

I wanted to do this, but gave up. Even if you can prevent, say a flagged column from being dragged, you still have the problem of preventing other columns from being dragged before it (thus moving it by side effect.) I'd be grateful for a solution, but I'm not holding my breath.

----

Do the columns need to be resized? If not you can easily fix this by telling the table view not to display column headers (which are used to drag), and then draw them yourself (either using an NSImageView or an NSView). It would be more difficult to implement if you have sorting set up, but if that is not the case, it would work out fine.

----

If anyone has anymore advice on this subject, please share! 

----

Probably the best you're going to get without a lot of hackery is to allow the drag to take place but put them back the way you want afterward. The visual result to the user would be that as soon as they drop the column, it'd be back where it was. Not pretty, but functional.

