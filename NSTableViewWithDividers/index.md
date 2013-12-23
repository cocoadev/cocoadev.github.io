---
layout: page
title: NSTableViewWithDividers
---

Does anyone have hints on how to create a table view with dividers like Disk Utility or Delicous Library use? (If you aren't familiar with those apps, I'm looking for the same visual effect as the sidebar in the Finder, but obviously that isn't using NSTableView.)

*See NSTableViewInFinderWindows*

As far as I can tell, that doesn't answer the question. The sample code makes a composite control with multiple table views, which isn't what I'm after. Did I miss something?

----

Are you looking to have the row height shrink and grow with resizing of the splitview?

----

If I properly understand what you're trying to do, check out this test I made.  It requires Tiger because it uses:

- (float)tableView:(NSTableView *)tableView heightOfRow:(int)row

It's kind of simple and hackish but the idea is there...

http://homepage.mac.com/austinsarner/dividingtableview.zip

and a screenshot:

http://homepage.mac.com/austinsarner/divider.jpg

If you're looking to target Panther though, I imagine it would become far more difficult.


----

Austin, thanks very much for the effort. I'm targetting Tiger and later, so it is ok from that regard. However, I'm using CoreData and bindings so adding a dummy row for the divider doesn't seem practical.

I want to do something along the lines of

tableView:shouldDrawDividerAfterRow:

and have the table view do all the magic. I'm not sure which methods to override though, besides the obvious ones for drawing.

*You could create a custom cell that subclasses NSTextFieldCell (or whatever cell you are using) that has a **divider** attribute. Override     drawWithFrame:inView: to draw a dark line, if the attribute is set. Finally, add a     tableView:willDisplayCell:forTableColumn:row: method to set or clear the **divider** for each cell. Good luck! --JediKnil*

If I go that route, which sounds doable, it leaves the problem that when selecting the cell which has the divider attribute set, the divider will be part of the selection color (since it is part of the row.) Ideas?

*I would go ahead and make the divider a genuine row.  So you can't bind the array controller directly to the CoreData data - big whup.  Insert an intermediary object that dynamically inserts dividers. In pseudocode,*

    
intermediary: object at index i:
    if i < dividerIndex
        return data object at index i;
    if i == dividerIndex
        return divider object
    if i > dividerIndex
        return data object at index i - 1

