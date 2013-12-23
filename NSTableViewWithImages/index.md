---
layout: page
title: NSTableViewWithImages
---

To make a column display an image in every row, you can change the cell type.  I have this in my awakeFromNib routine:

    
    /* change the first cell in the table to be a picture */
    {
        NSTableColumn* theColumn =[previousFileView tableColumnWithIdentifier:@"1"];
        [theColumn setDataCell:[[NSImageCell alloc] initImageCell:nil]]; 
    }


Then whenever I get the objectValueForTableColumn message for that column I just return an NSImage.

*This code will leak an image cell; not a big deal if this window only appears once in the program, but very important if it can be opened many times. Release the image cell after setting the column's data cell.*
----
I need an action that puts a image to a secelected NSTableView row. --JoshaChapmanDodson

----

If you want it in a particular row, only set that row to have the image displayed.
----
Could try file:///Developer/Examples/AppKit/DragNDropOutlineView/ImageAndTextCell.h

