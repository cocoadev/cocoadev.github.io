---
layout: page
title: ClickingBoundCustomNSCellRedrawsLastCellClicked
---

I'm having a problem where I'm not sure whether I'm doing something wrong or bindings is doing something wrong.  I have a custom NSCell (nothing too fancy, the normal image+text stuff).  In Interface Builder, I've got the table column bound to NSArrayController.arrangedObjects.  In the code, however, I build my cell and set it to the data cell of the table view.  I then bind all of my outlets to the object value.

    
CustomCell *cell = [[CustomCell new] autorelease]; // It isn't really named "CustomCell", but this is an example
[[[TableView tableColumns] lastObject] setDataCell:cell]; // There's only one column
[cell bind:@"image" toObject:cell withKeyPath:@"objectValue.image" options:nil];
[cell bind:@"text" toObject:cell withKeyPath:@"objectValue.name" options:nil];


Everything works just peachy except for when I select a table row.  The row changes to be the last row clicked instead of highlighting the current row.  For example, if I'm currently highlighting row 2 and I click row 5, row 5 is redrawn to be row 2.  Row 5 is correct again (and correctly highlighted) when I release the mouse button.  Where am I going wrong?

