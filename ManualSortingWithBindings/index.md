---
layout: page
title: ManualSortingWithBindings
---



11/28/2004

I have an application that makes extensive use of CocoaBindings. I want my NSTableView to continue to benefit from the automatic sorting that CocoaBindings gives it, but I also want users to be able to manually re-order rows as in an iTunes playlist. How could I accomplish this while saving this manual sort order? I'd imagine the moment a user clicks to sort by a column, the order is simply dropped, but what happens when they drag-reorder it? How do I store this order? How do I prevent CocoaBindings from changing the order?

----

Possible (untested) solution: Create a new column that has numerical data, like in iTunes. When users click on that row, sort by the numbers (which represent your custom order). Then add DND capability for dragging rows (I won't go into that here, but I think you can look at DragAndDropTableView and get some tips). Set the result of dragging rows (what happens when you drag them) to update the index numbers of every row between the source row and its destination. Since you're using bindings, just log all changes to NSUserDefaults. This won't work if you have the same item in multiple tables, but you should save on a table-by-table basis instead in that case (by assigning a unique ID number to each track and saving an array of ID numbers for each table).

Hope that helps -- JediKnil

----

Thanks for the suggestion. The problem is that I don't want to have an extra column with numbers cluttering up the interface. This is a good idea, though - the model object can contain a "sortOrder" IVAR. The biggest thing that's confusing me is how to prevent my NSArrayController from overriding the sorting if I'd do this ... how to even implement the mechanics of the sortOrder property.

----

Easy - don't add the column to the table. Just implement DnD as usual and manually renumber each row according to the new dragged order. You can have it set the sort descriptors to use the 'sortOrder' the moment a drag is successfully completed. I don't know how you'd want to handle going back to the manual order if a user clicks another column header then wants their manual order back again ... A button near the table that says, "Sort it the way I sorted it before" doesn't sound too appealing. ;-)

