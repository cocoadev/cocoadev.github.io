---
layout: page
title: CompositeCell
---

I would like to build a cell to use in a table view that has multiple controls in it, except that it seems to be better to use cells, otherwise I'd just stack a bunch of views together in a scrollview and call it a day. Since that's heavy-weight and I'm all for lightweight, I thought about a cell that used other cells to draw it's parts in an attractive layout, along with images.

I'm looking for something like that seen in the Mac OS X Bittorrent client, which is called a DLCell -- except that class indeed seems to use actual control objects and possibly even an NSBox as it's content, thereby defeating the purpose of a cell (except of course that the cell can easily be put into a table).

If I create a custom NSCell subclass and add a bunch of instances and do the layout programmatically, would that be enough? Are there any pitfalls? I found an old article on stepwise by Erik Buck which describes a composite cell, so I have something to start with, but a current example would be appreciated if anyone has done this and has something that they can share. � BrentGulanowski

----

brent, 
I strongly suggest doing this in a Control and use the control as the parent to your various cells.  Why? well Cells all Have to live inside a Control (they can't draw themselves outside of a View Calss, and they rely on the control to do alot of the work), and will therefore be dependent on the control regardless of what you do.  Its the "natural" way to do this and you already have some of the support you will need if you go with a subclass of NSControl.  NSTable for instance is a specialized control that displays Multiple Cells in a grid like pattern with some sort of display order.  But you aren't limited by the Control classes you see in the app kit, you could for instance, subclass NSControl to control multiple NSCells that you place programatically.  This is what you Should do.  take a closer look at what controls and cells do.

I'm sorry but that won't do because I'm putting the cells inside an existing control: an NSMatrix (inherently included in the document view of an NSTable). I can't make the table view accept a control in place of a cell. Even if that weren't true, I'd still want to make a Cell to contain the functionality, which I would then wrap into a control of my own devising. And I assure you that the Table view is exactly what I want as my foundation and don't want to roll a nearly identical (if less full-featured) class on my own.

----

then here is how to make this lightweight. and use whatever views or controls you think you need.
NSviews can have subviews, 
NSTables are subclasses of NSView.
NSTable can't have Views as Cells, and Views are necessarily slower than cells anyway, if we populated a table with Views it would crawl.
so what we need is, a view Only where we need to change values in the table.  So if the controls are laid out the same in each Cell, the answer is simple:

subclass NSTable, fill it with imageCells.
make a control hierarchy... a NSView of some sort with controls in it you should know best what you need here (we'll refer to it as YourControlClass).
keep track of the selected Cell and the ref to YourControlClass, in your table subclass.
when there is a new selection in the table, take an image of YourControlClass's view, and give it to the old selected imageCell, and move your view so that it superimposes the image of the selected cell.

this keeps the View Overhead down to a minimum, while still giving you the View hierarchy you need.  it takes some work to manage, but its probably worth it.  It also make it unnecessary to develop your own NSControl, from scratch

----

See http://www.stepwise.com/Articles/Technical/NSCell.html for why the above poster is wrong and obviously didn't read the whole post.

----

So basic composite cells aren't that big a deal if all you want is to draw. I got a custom cell with text field and image cells working fine. One thing I cannot figure out (but don't need to do anyway) is to make the embedded cells behave as they normally would in a standard Cocoa control. For example, I cannot get the text field to be editable. Passing along a - startTrackingAt:inView: just gets a "NO" back right away. It would make me feel like I had a much better understanding of cells to be know how to do it. And something about table views (or table columns, I guess) -- why do they copy and discard the cell they are displaying every time you click on one? That seems wasteful, but I suppose there must be a reason.

----

As of such, the cell cannot initiate any editing, it can "capture" the mouse, but for textual editing, it relies on the control to send it editWithFrame:inView:editor:delegate:event:, and then it should bring up the field editor to do the actual editing (that is, it will add a new NSView (subclass) to its control view, which it will setup as first responder).

A am also puzzled by the need to copy the cell on each single mouse click -- perhaps it's something about being sure that the cell is the same during the click and potential drag (e.g. to create a drag image), and since cells are so lightweight ;) the implementor of the table view just did a copy instead of a retain...

----

Well I can report that I have successfully made the text in the interior cell sort of editable by passing the text editing messages on to the interior cell when the mouse location for the double click is within its frame. However, when editing, new text is drawn on top of the old text. I cannot select the text.

The text editing messages are odd. I probably need to learn more about the text architecture. I would have expected the text cell to find and negotiate with the field editor on its own, not to be told how to behave by some other object.

If I can even get this to work I have to figure out how to recover the new text. Perhaps the interior cell can send an action to it's container cell which can forward it on in such a way that the table sends -tableView:setObjectValue: to the delegate, which can sort it out in some way. Might end up needing a smarter table view which understands composite cells and can send some information about the interior cell which was selected.

