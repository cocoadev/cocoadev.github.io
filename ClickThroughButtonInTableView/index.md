---
layout: page
title: ClickThroughButtonInTableView
---

Time to give back to the Cocoa communauty.

Here is a piece of code to add click-through button (like in itunes or safari (download panel)) in a tableview
it's not perfect but it was a good way to learn

use:

MCTableView => custom NSTableView class to manage the cell and mouse event
MCButtonCell => custom NSActionCell class to draw the cell

sample use:
    
 
theCell=[[MCButtonCell alloc]init];
[theCell setImage:[NSImage imageNamed:@"arrow.tiff"]];
[theCell setAlternateImage:[NSImage imageNamed:@"arrowdown.tiff"]];
myTableView tableColumnWithIdentifier:@"correspondantspd.Nom"] setDataCell:theCell];



to detect click-through button action use in you action @selector for the tableview the [sender clickedButton] value.

if ==-1 no click , else return the value of the "clicked" row


Comments are welcome.

(known bug : the cell is not well displayed when a date formatter is added to it)

a screencap:

http://photos1.blogger.com/blogger/6606/1806/1600/screencap1.jpg

----

**Overkill?**

At first glance, I may be missing something ... but ... this seems like a case of killing a flea with a cannon ball. This can be done with a borderless button set as the data cell for a column. I do it that way in several shipping applications right now. To have the clicked row selected immediately (which I believe is one of the goals you were trying to accomplish), a simple subclass of [[NSTableView will take care of that (there are several examples floating around, I believe).

----
Looks like MCTableView inherited some code from NSTableViewRollover that doesn't get used.     mouseOverRow should return the row the mouse is over, or -1. I'm glad it was at least somewhat helpful but breaking the implementation and keeping the interface is...weird. -- RyanStevens

----
Actually, I *would* like to post a retraction of the above paragraph. It looks like there's text (with ellipses-style truncation) followed by the button in the same cell. I apologize for not having looked more closely before commenting. <:-}

----
Apologies to RyanStevens, in fact the NSTableViewRollover code is not used for the moment, because of a bug in my implementation when resizing the tableview (which make it look like it feeze). I have cleaned the code for MCTableView

The title of this page is not perfect, in fact i should have choose clickthroughimageintableview. The cell is composed of an icon (on the left but easily hacked) and a text.

----
I've done similar, it's all good. What happens if you make MCTableView a subclass of ROTableView (NSTableViewRollover), do you still get the freezing? Just curious..

