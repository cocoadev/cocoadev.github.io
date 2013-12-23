---
layout: page
title: NSMatrix
---

An NSMatrix is vaguely similar to an NSTableView in that it allows you to arrange various cells in a spreadsheet-like table. NSMatrix works by using subclasses of NSCell and laying them out in rows and columns.

See Apple's documentation at: http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/Classes/NSMatrix_Class/index.html#//apple_ref/doc/uid/TP40004060

[Topic]

----
If you need a matrix of image view's that can be sorted by the user (similar to an outline view) then take a look at DragMatrix.
----

Question: How do I use an NSMatrix so that each button can independently be on/off? How do I set the state of the individual cells?
----
Answer: Each button is independent in a matrix.  Use -cellAtRow:column: to access individual cells.
----

Question: I created an NSMatrix of NSImageView cells in InterfaceBuilder, and made it an outlet in my controller.  Do I need to allocate space for and initialize the NSMatrix and its contents?
----
Answer: No.  Any object in your nib file will be automatically allocated/initialized when that nib file is loaded.
----

Question: When I send an NSMatrix a release message, does it send this message to all its cells?
----
Answer: Yes.  It release all its cells on its own.  However, you generally don't need to deallocate things you loaded from a nib as the window controller will take care of releasing the nib's contents when it gets deallocated.
----

Question: I created an NSMatrix in InterfaceBuilder, how can I tell it to use a custom NSCell type, one that I subclassed to make it incrementally load an image as I create it.  Or should I use delegates?
----
A: There seems to be many ways for you to do that. If you just want to replace some cells you can use the NSMatrix method     - (void)putCell:(NSCell *)newCell atRow:(int)row column:(int)column.

You can also try to create your NSMatrix programmatically with     - (id)initWithFrame:(NSRect)frameRect mode:(int)aMode **prototype:(NSCell *)aCell** numberOfRows:(int)numRows numberOfColumns:(int)numColumns
--EnglaBenny
----
----
A2: I found that     - (void)setCellClass:(Class)aClass doesn't work because InterfaceBuilder creates the NSMatrix with a prototype cell instead of a class.  You have to use     - (void)setPrototype:(NSCell *)aCell instead.

To set a custom class for individual cells you just double-click the cell to select it and set the class on the Custom Class page of the Inspector for that cell.
Once you have one cell of the correct class, you can set it as your prototype in     - (void)awakeFromNib or wherever you need it.
--John Dalbec
----

Question:  Is there a way to make certain cells of an NSMatrix greenColor and others blueColor, for example?  If so, how do you set the color of each cell individually? --AlexanderD
----
IIRC, you need to use the delegate to arrange this: since there's actually only one cell involved; you need to change its color to match the location it's placed at. Unfortunately, I can't find what methods NSMatrix's delegate will be sent.
----
----
The delegate methods are documented in the NSControl class reference (NSMatrix's superclass).
--John Dalbec
----

Question: I'm trying to get the highlight color to be something other than dark grey, yet I can't seem to do this with cellBackground color or backgroundColor.  Any ideas?
----
Answer: Override     -(NSColor *)highlightColorWithFrame:(NSRect)rect inView:(NSView *)controlView and return the color you want.
----

Question: Why can't NSCells be hidden?  If I have an NSMatrix of NSTextCells, why can't I hide two of them?
----
Answer: NSCells are not NSViews. Try obtaining the view with -controlView, then hide that.
----

Question: How exactly do you make an NSMatrix of NSImageView cells in IB? I can make one with radio and other buttons, but I have no idea how to get the cells to be NSImageView cells in IB....
----
Answer: Take the NSImageView template and drag it into your window (or view, or whatever). Then hold the option key and drag the corner out (the view will be converted to a matrix of image cells). This should work for any control that has a corresponding cell class.
----

I have an NSMatrix of NSButton cells with a scalable image on each cell, but I'm not able to get the images to scale as the NSMatrix resizes.  I'm using the following code to set the image on the NSButton cell that is clicked:

    
- (IBAction)cellClicked:(id)sender
{
	id cell = [sender selectedCell];
	NSButtonCell *button = (NSButtonCell *)cell;
	NSImage *image = [NSImage imageNamed:@"image.png"];
	[image setScalesWhenResized:YES];
	[button setImage:image];
}


----
Answer (11/20/2004)

    - (void)setAutosizesCells:(BOOL)flag --zootbobbalu
----

----
Question (1/23/05): Can I set it so that an NSMatrix of NSButton cells has *no* selection capabilities whatsoever? This would mean that clicking on a cell would do nothing. Alternately, does anyone know how to disable NSButton cells without having them greyed out? (This is equivalent to no selection if all cells are disabled) --JediKnil

----
Question (6/21/2005): I'm trying to use a NSMatrix of NSImage cells with bindings. I create the matrix of NSImage cells.  However, the NSMatrix bindings doesn't have anywhere to place what the content object for the matrix should be.  However, if I create a matrix of NSButton  cells, the NSMatrix bindings has a place for contentArray.  How I can have an NSMatrix use a NSArrayController as its content?
 
Answer (10/25/2007): I'm amazed that this question doesn't come up more often! Binding a matrix of NSTextFieldCells to an array controller would seem like a fairly common thing, and the answer is neither obvious nor (as far as I can see) documented.

In any case, the thing you need to know is that the available bindings for a NSMatrix object vary depending on the object's selection mode. Furthermore, the selection mode that Interface Builder chooses when you create your matrix depends on what type of cell you're using. If you create a matrix of push buttons, IB chooses the "highlight" selection mode for you. If you create a matrix of radio buttons, IB chooses the "radio" mode. These modes make 14 and 16 different bindings available, respectively, and both include the bindings for 'content', 'contentObjects', 'contentValues', and so forth. However, if you create a matrix of text fields, IB chooses the 'track' selection mode for you, and this mode doesn't have the various content bindings. Therefore, if you want to populate a matrix of text cells by binding it to an array controller, change the selection mode to one of the modes other than 'track'. You'll then see that the bindings you need are available. -CS
----

Question (6/26/2005): How do you actually make an NSMatrix? I see no way to make one of them in IB. I'd love to use the NSMatrix, but without the ability to drag one onto a window, all of its features are lost to me. I'm not seeing NSMatrix on the palette. I see NSOutlineView, NSTableView, NSBrowser in the palette, but no NSMatrix.
----
Answer: This is another one of those hidden features. First, place an object in your window that you want to be the component of your matrix. If you want a matrix of buttons, place a button. If you want a matrix of text fields, place a text field. Once you've done that, select your new object, hold down option, and resize it. Instead of actually changing the size, it will look like you're creating copies. This changes the object into an NSMatrix, and the "copies" are actually the individual cells in the matrix.
Answer 2:  This doesn't appear to work in IB3 (or I'm doing it wrong), but I've just found that if you select an object in your window you can then choose Layout->Embed Objects In->Matrix from the menu to wrap the object in an NSMatrix
----
----
8/16/2006 What about a custom class? I can't see a way to create an NSMatrix of my own class that way.
----

Question (6/27/2005):  This isn't immediately obvious to me. What situations would I use an NSTableView instead of an NSMatrix? And in what situations would NSMatrix be better than NSTableView?
----
Answer (12/5/2005):  An NSMatrix is like a spreadsheet; you can select a single cell or discontiguous group of cells regardless of row/column, whereas NSTableView only allows selection of entire rows and entire columns.
----

Question (01/04/2006): Is it possible to have cells that span multiple columns or rows in an NSMatrix?
----
Answer (01/04/2006): [ Short ] No. [ Longer ] No, not without *serious* subclassing which suggests rolling your own control would be best.
----

Question: Is it possible to delete a Particular Cell from NSMatrix.
----
Answer: Can you describe what you mean by delete?  It doesn't make sense to completely remove a (row,col) point in a matrix without also removing the entire row and column.  If you want to replace a cell with a fresh cell of some sort, you can use putCell:atRow:column:.
----
Huh? Why doesn't it make sense? Makes perfect sense to me, *in certain circumstances*. A matrix containing a set of "favourites" for example, where you decide you want to get rid of one. Probably countless other examples. --GC

Answer (10/25/2007):I don't believe it's possible to configure a matrix such that there's no cell at a given location, but you can get the effect you're looking for by replacing the existing cell with a new cell that doesn't draw anything. You don't need to create a custom NSCell subclass for this, just use NSCell itself. You'll want to disable the new cell as well, so that the user doesn't get feedback when they click in that spot. A bit of code follows. -CS
    
id blankCell = [[[NSCell alloc] init] autorelease];
[blankCell setEnabled:NO];
[matrix putCell:blankCell
             atRow:1
            column:0];

----
Sorry to dig this dusty page up again, but...

Question (01/11/2007): Is it possible to alternate between two cell classes in a matrix's rows? Specifically, a custom cell class on all odd rows, and an NSTextFieldCell on the even rows. Fortunately, my matrix instance only has one column.

Answer (10/25/2007): Yes, it's possible. You can actually set the class of an individual cell in IB by selecting the cell and choosing a custom class in the IB inspector palette. It might be more maintainable, however, if you just make the matrix using all one class of cells in IB and then programmatically replace the cell on each odd row in your -awakeFromNib method. You can replace a cell using the -putCell:atRow:column: method of NSMatrix. -CS
----

Question (9/11/2007):I have each of cells of an NSMatrix set up to trigger the same action. Do I need to subclass NSMatrix to find out what cell was clicked?
--Darknoon.
----
Answer: No, the cell that was clicked is *nearly* always the selected cell. For some unusual cell/selection mode/user input combinations, it might in theory be the "key cell", which is the cell that receives key events and is nearly always the same as the selected cell. Most of the time the selection is fine, especially if your action is easily undoable.

----
Please use the forums for further questions.

