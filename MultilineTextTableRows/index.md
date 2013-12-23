---
layout: page
title: MultilineTextTableRows
---

Hi all,
I was wondering if there's a relatively simple way of having an NSTableView (or NSOutlineView) where the rows rather than being single lines, can hold multiple lines of text.  I know NSTableView can have variably sized rows, but it's the jamming of an NSTextArea into the rows that I'm unsure of.  If this is easy can someone give me some pointers, and if it's hard, can someone give some more pointers? :)  I'm hoping in the end for something along the lines of the OmniOutliner outline view.  I'm assuming it will involve changing the field editor but I'm not so up on how to do that.

----
Use **setLineBreakMode:** method to dataCell of column in wich multilines textField must appear.

On entire column, at initializatiom time (awakeFromNib) make a :
    
[tabeViewOutlet tableColumnWithIdentifier:@"yourIden"] dataCell] setLineBreakMode:[[NSLineBreakByWordWrapping];

(make sure rows are big to display entire multilines textField)

Or at delegate time, mix **tableView:heightOfRow:** and ** tableView:willDisplayCell:forTableColumn:row:** to display text in multines for one cell in one row.

Bru.

----
Hey Bru,
Thanks for the reply!  It's so close!  It seems that this will only wrap if the line is too long.  I was hoping to be able to hit return (or something else) and have a new line begin, but within the same table row.  Also, is there a way of getting the number of pixels tall a bunch of text should be so I can appropriately set the row height?

Thanks!

----
Simply type ALT + RETURN to insert a new line in the active edited textField (RETURN is used to validate entry).

To get height of a text, use **boundingRectWithSize:options:attributes:** or **sizeWithAttributes:** from NSString Additions.

Bru.

