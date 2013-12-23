---
layout: page
title: PrintingTableViewColumnHeaders
---

I have a long, wide table spanning two pages which I wish to print in a  Document application.  I use the code below straight out of Hillegass (2d Ed).  It works flawlessly in landscape mode producing two perfectly printed pages EXCEPT that the column header titles are not printed although they are part of the table view.   

How can I get the column header titles to print? (I don't want to print the entire window because it contains irrelevant stuff.)

Paul

******************'

- (void)doActualPrinting:(BOOL)flag

{

	NSPrintInfo  *printInfo = [self printInfo];
	
	NSPrintOperation *printOp;
	
	printOp = [NSPrintOperation printOperationWithView:tableView printInfo:printInfo];

	[printOp setShowPanels:flag];
	
	[self runModalPrintOperation:printOp delegate:nil didRunSelector:NULL contextInfo:NULL];


}


The printing is triggered by the action: [self doActualPrinting:TRUE];


----

The problem is that it doesn't print the headers. The table needs several pages and some lines of the table are cut, half in a page and half in the next page.

----

As far as I know, the header problem isn't too easy to surmount, as NSTableView is actually made up of two main views, the table itself, and the header view. Priniting only automatically supports printing from the table part. Someone else may be able to shed light on hacking something together that works though.

Good luck.

----

I haven't tried 'cause I don't have a printer, but what about connecting a button's or menu item's action to the print: action of the tableview's surrounding NSScrollView?

You could just go to Print an the view the preview if you dont have a printer.

----

Calling     print: on NSScrollView will print the headers, but it will also print the scroll bars and only the visible area of the table (which is probably not what you want). I would suggest creating an NSView subclass for printing the contents. It's a bit more work, but that way you have complete control as to how it will look. -- RyanBates

