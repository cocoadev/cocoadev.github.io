---
layout: page
title: UpdatingSingleNSTableViewRow
---



Is it possible to tell a tableview to update a single row somehow? I am using  bindings currently. I am having to call reloadData every half a second, as you can imagine, that is a major toll on the CPU in a large table. Help!!!?

----

*noteNumberOfRowsChanged*

----

That is unrelated to the problem, however I did find a way to do it:

    
unsigned row = General/itemsController arrangedObjects] indexOfObject:convertingItem];
[[NSRect updateRect = [moviesTableView rectOfRow:row];
if(!General/NSEqualRects(updateRect, General/NSZeroRect)) [moviesTableView setNeedsDisplayInRect:updateRect];


