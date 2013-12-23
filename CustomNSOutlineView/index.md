---
layout: page
title: CustomNSOutlineView
---



I would like to create a custom NSOutlineView where I would control the look of the items but I don't know where to start to accomplish this. Also, can I use a custom NSView as an item? Where can I find some info/tutorial/code sample to learn how to do this? Do I have to create my own custom view or I can start with NSOutlineView and tell it how to display its items?

----

Subclass NSCell, override its drawWithFrame method to do your custom drawing, and do a setDataCell on the table column in which you want the custom cell to be located.

-TylerStromberg

----

Do I need to set a NSCell for every item? If so, where/when do I use setDataCell?

----

You should call setDataCell: on the NSTableColumn which you want to modify. NSTableView (and therefore NSOutlineView as well) uses a single NSCell to draw each row in a column. So, you just have to give the column in question an instance of the NSCell you want it to use. You can access the relavent table column using:

    
NSTableColumn *column = outlineView tableColumns] objectAtIndex:0] // or whatever index you need

or, you can use:
    
[[NSTableColumn *column = [outlineView tableColumnWithIdentifier:@"yourIdentifierHere"]; // change the identifier appropriately


I hope that helps!

- MattBall

