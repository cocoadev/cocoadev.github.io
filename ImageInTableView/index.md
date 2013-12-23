---
layout: page
title: ImageInTableView
---

Its pretty easy to setDataCell on an entire column in NSTableView but to add images as the table is updated, should you not create a cell and then add the cell using this method:

    

- (void)tableView:(NSTableView *)aTableView willDisplayCell:(id)aCell forTableColumn:(NSTableColumn *)aTableColumn row:(int)rowIndex
{
    if (aTableColumn identifier] isEqualToString:@"icon"])
    {
        aCell = icon;
    }
}



Obviously this doesn't work or I wouldn't be here....so how can I make it work? I am fairly sure that my cell is created correctly and the same with the [[NSImage in it.

----

I don't know enough to tell you how to get an icon to display in an NSTableView (I've never done it, at least)-- but I can tell you why the code above doesn't work: you're merely setting the aCell variable to the address of your icon within the scope of this method. E.g., *outside* of the method, aCell still points to whatever it originally pointed to. You're probably going to have to somehow tell the cell to draw your icon, probably through [NSCell setImage: (NSImage *) image]

Better yet, set the cell for the column you're interested in to an NSImageCell.
--ShamylZakariya

----

Please look for the DragNDropOutlineView example in your developer/examples folder.

