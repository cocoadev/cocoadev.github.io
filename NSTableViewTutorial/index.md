---
layout: page
title: NSTableViewTutorial
---



----

Cocoa provides a great class for doing simple tabular interfaces: NSTableView. This tutorial assumes you understand how to set up a simple nib file and are familiar with Cocoa's      /view/controller concepts. If this isn't the case, you'll want to go through a more elementary tutorial, such as the ubiquitous CurrencyConverter tutorial before continuing here. 

NSTableView is easy to get started with, so let's get started. 

== Lesson 0: Setting up your nib file ==

You will need to do a little extra work when adding a table view to a nib file: you need to hook up your controller object as the table view's dataSource. You do this by control-dragging from your table view to your controller object. Once this connection is established, the table view will ask your controller for new data, notify your controller when data changes, and so on. A full description of the messages sent to the dataSource are described in the AppKit's protocol documentation for NSTableDataSource.

While you're in there, you should hook up your controller as the table view's delegate, again by control-dragging from the table view to the controller. This way you can respond to table delegate methods as well. Delegate messages are described in the AppKit's class documentation for NSTableView.


== Lesson 1: Get something in a table ==

First let's try to get some data into our table view. As an example, I'm using an array built by CalumRobinson's ListOfConnectediPods example.

Our controller has an ivar named iPodVolumes which is an NSArray of NSStrings. We want to display the strings in a table view with one column. We simply add the following code to our controller:

    
 - (int)numberOfRowsInTableView:(NSTableView *)tableView
 {
     return [iPodVolumes count];
 }
 
 - (id)tableView:(NSTableView *)tableView
       objectValueForTableColumn:(NSTableColumn *)tableColumn
       row:(int)row
 {
     return [iPodVolumes objectAtIndex:row];
 }


When you build and run the program, you'll see the list of connected iPods appear in your table. If you don't see the list, make sure your nib is hooked up properly. Also make sure your iPod is plugged in ;-)

Why do these methods all pass in 'tableView'? Your controller may need to respond to messages from more than one table view. By checking the value of 'tableView' your program can return different results for each table. For example:

    
 - (int)numberOfRowsInTableView:(NSTableView *)tableView
 {
     if (tableView == iPodTable) {
         return [iPodVolumes count];
     if (tableView == songsTable) {
         return [songs count];
     } else {
         return 0;
     }
 }


Careful programmers might also compare the value of 'row' against the array count within     -tableView:objectValueForTableColumn:row: to make extra sure something isn't about to request an object that isn't in your array.

If the contents of your data source change, for example if the user adds/removes an iPod, you'll need to update your table. You can do that from your controller with a the simple command:

    
 [tableView reloadData];


This instructs the table view to invalidate itself and load in new data from the data source.

Finding out which row is selected is a simple matter. Since NSTableView is a control, you can also respond to the control changes directly. That way something can happen as soon as the user selects row in the table view. We'll do that by defining an action, and hooking the action up to our table in our nib file:

    
 - (IBAction)tableViewSelected:(id)sender
 {
     row = [sender selectedRow];
     NSLog(@"the user just clicked on row %d", row);
 }


Of course, your controller can call NSTableView's -selectedRow method at any time.


== Lesson 2: Editable tables ==

Basically, editing tableviews is as easy as adding one more helper method to your delegate:

    
 - (void)tableView:(NSTableView *)aTableView setObjectValue:(id)anObject
    forTableColumn:(NSTableColumn *)aTableColumn row:(int)rowIndex


To use this, simply have the array that you use to display the rows and columns mutable so you can chop and change items at will. 

For example:

    
 - (void)tableView:(NSTableView *)aTableView setObjectValue:(id)anObject
    forTableColumn:(NSTableColumn *)aTableColumn row:(int)rowIndex
 {
     [items replaceObjectAtIndex:rowIndex withObject:anObject];
 }


Will replace the relevant object in items with the new object that has been sent.

The above helper is called when return is hit or when the table stops editing. The new value of the cell is anObject, the row of the cell is rowIndex and the column of the cell is aTableColumn.

To add an item so that it is editing (aka adding a playlist in iTunes) you can do this:

    
 - (IBAction)add:(id)sender
 {
     [items addObject:@"Untitled"];
     [table reloadData];
     [table selectRow:([items count] - 1) byExtendingSelection:NO];
     [table editColumn:0 row:([items count] - 1) withEvent:nil select:YES];
 }


You need to make sure the row is selected (carbon apps don't have to which is why you may see it differently in iTunes, etc.) and then run an     editColumn:row:withEvent:select: on it. You can send whatever event you want to it, but I usually send nil as I don't do anything special with my delegate behind usually. 

My explanation might be a little wonky, so if you need some questions answered, post away. -- MatPeterson

    
 - (IBAction)remove:(id)sender;
 {
    if ([table selectedRow] < 0 || [table selectedRow] >= [items count])
       return;
    [items removeObjectAtIndex:[table selectedRow]];
    [table reloadData];
 }


Thats how you remove a row -- JoshuaPenn


== Lesson 3: Customizing row/cell display ==

[coming soon] - I'd love to see something about changing fonts (specifically making the text smaller Àla iTunes). - NateClinton

To make the font smaller, simply select the tableview in InterfaceBuilder and choose the font to use via the Show Fonts (Cmd-T) requester -- you may need to try it a few times before InterfaceBuilder actually gets it... perhaps something about selecting a table column also, selecting the actual NSTableView instead of the NSScrollView or similar, I have never really found the system :-)

Changing fonts programmatically is not too hard either - suppose you have a preference pane that selects a font name & size, then posts a notification.
The notification is captured in the controller class that's responsible for the table view:

    
 - (void)handleFontChangedNotification:(NSNotification *)notification{
     NSFont *font = [NSFont fontWithName:fontNameFromPrefs size:fontSizeFromPrefs];
     
     // adjust the height of the rows: (sometimes this isn't quite right.)
     [tableView setRowHeight:[font defaultLineHeightForFont]+2];
     [tableView setFont:font];
     [tableView reloadData];
 }


NSTableView -setFont is not in AppKit, it's from the OmniFrameworks, and looks like this:

    
 - (void)setFont:(NSFont *)font;
 {
     NSArray *tableColumns;
     unsigned int columnIndex;
     
     tableColumns = [self tableColumns];
     columnIndex = [tableColumns count];
     while (columnIndex--)
         (NSTableColumn *)[tableColumns objectAtIndex:columnIndex] dataCell] setFont:font];
 }


And there you go. -[[MichaelMcCracken

As an aside, if you want to do something like iTunes, and have a small icon next to your text in the table, without adding an extra column, you need to create a subclass of NSCell (or one of its subclasses) and then draw in the icon and text by hand. Some hints when doing this:                                       

- Make sure you abide by the standard highlighting (gray when out of focus, blue when in focus) or whichever colors you want. Here is some code to do that:

    
 if ([self isHighlighted] && [[self controlView] window] firstResponder] isEqual:[self controlView)
   { string = [string whiteAttributedString]; }


It seems to work fine for me, but it may be inefficient.

- If you can, get the value of the fonts from the tableview's settings so that you don't have to do silly changes when you move your subclass between projects

- Do not fix your text positions, make sure you adjust them on the fly in regards to the fonts height. Normal table cells seem to adjust via the latter

- If you don't need to redraw the cell, don't, its a waste of resources especially in tableview's.

Hope this helps -- MatPeterson
                                                  
----

I recommend doing the least work possible. For this: subclass NSTextFieldCell and override only the drawWithFrame method, making sure to call the super class for the full implementation. The following is a sample to add a button to the right side of the cell:

I'd recommend using this instead of rolling out features that emulate what the superclass would be doing anyways. As such, this code is very maintainable. You will inherit all upgrades to NSTextFieldCell for free.

                                                                                         
 -(void)awakeFromNib
 {
   button = NSButton alloc] init];
   [[self controlView] addSubview:button]; 
 }
 
 - (void)drawWithFrame:(NSRect)cellFrame inView:(NSView*)controlView
 {
   NSRect buttonFrame = cellFrame;
  
   buttonFrame.size.width = 20;
   buttonFrame.origin.x += cellFrame.size.width - 20;
  
   [button setFrame:buttonFrame];
  
   cellFrame.size.width -= 20;
   [super drawWithFrame:cellFrame inView:controlView];
 }



== Lesson 4: Customizing column display ==

You can customize a column's data cell allowing you to put [[NSButtonCells, NSPopUpMenuCells, etc into the table view. For instance to add a Button the following code could be used:

    
 - (void)awakeFromNib
 {
   NSTableColumn *desiredColumn;
 
     NSButtonCell *cell;
     cell = NSButtonCell alloc] init];
     [cell setButtonType:NSSwitchButton];
     [cell setTitle:@"Im a Button!"];
     [cell setAction:@selector(buttonAction:)];
     [cell setTarget:self];
 
   desiredColumn = [tableView tableColumnWithIdentifier:@"buttonColumn"];
   [desiredColumn setDataCell:cell];
   [cell release];
   
 }
 
 -(IBAction)buttonAction:(id)sender
 {
   NSLog(@"A button has been clicked");
 }


Now the column whose identifier is "buttonColumn" will display a [[NSSwitchButton which when clicked will call the buttonAction method.

The process is basically the same for adding most other cell types. It is important to note that the delegate methods which interface with the data source should return the following object values:


*NSButtonCell - NSNumber value containing a 1 or 0 to represent state
*NSPopUpMenuCell - NSNumber value containing index of menu item to display


== Lesson 5: Automatically resizing ==

[coming soon] - when you get to this, what about proportional resizing of column widths, like Mail does. I've tried     setAutoresizesAllColumnsToFit: which says that it sizes proportionally, but that doesn't seem to be working.


== Lesson 6: Adding a "search" field and live filtering ==

To create an iTunes-like "Search" text field into which you can enter some text to have the items in the NSTableView below it filtered "live" (i.e. items that don't match the search term are removed from the list), use a second array.

There is a very detailed article at Oreilly's MacDevCenter about this:  http://www.macdevcenter.com/pub/a/mac/2002/03/15/cocoa.html - the interesting bits start on Page 4.

The Reader's Digest version with some clarifications follows:

Usually, you have a single NSMutableArray that contains all items for your list. To allow searching, simply create a second NSMutableArray that holds all search results. Since NSArrays hold references to the objects, both arrays will point to the same list items, but the second array will simply refer to only part of the items.

As long as the filter string isn't empty, have your data source return the item count and items of this second array. You can do this e.g. by adding an additional NSMutableArray variable (called e.g. activeSet) that you make point to the sub-array you want to show in the list, and have your data source always refer to activeSet.

You have to be careful when deleting items, though, as you have to make sure both lists are in sync. Especially take care that:
1) Since both arrays retain the objects, make sure you remove an item from both arrays, or the item will still be around in the list that wasn't current.
2) item indexes into the sub-array will obviously be different than those in the full array. You can use removeObject: to remove an item from the array by pointer, as long as each object may only appear in the array once.
3) adding a new item means that if it isn't filtered out, you may also have to insert it into the second array

Changing items doesn't require any special work, since the actual objects both lists refer to are the same.


== Wrapping up ==

That concludes our tutorial on NSTableView. Go nuts with it.

Contributors: MikeTrent, GormanChristian, UliKusterer

----

See also http://developer.apple.com/techpubs/macosx/Cocoa/TasksAndConcepts/ProgrammingTopics/TableView/index.html

----

I need to know how to cause action methods to be sent by scrolling with the arrow keys. I guess I can make a category, but isn't there anything built in? You know, so I can update the contents of another view, like in Mail. Something like, -sendActionOnSelect: (which doesn't exist that I can find).

Try using :
"    NSTableViewSelectionDidChangeNotification
 Posted after an NSTableView's selection changes. The notification object is the NSTableView whose selection changed. This notification does not contain a userInfo dictionary"
----
Your example is cool. But what for the people who have not bought a iPod yet. Could you show us how to display regular volumes? --JoshaChapmanDodson
----
Change
    
 NSArray *allVolumes = NSWorkspace sharedWorkspace] mountedRemovableMedia]
to
    
 NSArray *allVolumes = [[NSWorkspace sharedWorkspace] mountedLocalVolumePaths];

----
How can I get the reference to a single cell in a [[NSTableView ? For example, make the text of one cell turn red...
----
When setting the value of the cell, make it red. :)
----
Well, I mean, during runtime, and programmatically...

*When setting the value of the cell during runtime, make it red*

NSTableView has a delegate method     - (void)tableView:(NSTableView *)aTableView willDisplayCell:(id)aCell forTableColumn:(NSTableColumn *)aTableColumn row:(int)rowIndex that is called for each cell that is drawn. You can implement this to change the display attributes of the cell. Note that since the cell is reused for each row in the table column you must set the display attributes both when drawing special cells and when drawing normal cells.
----
Is it possible to read arrayA in to column one and arrayB into column 2 given a 2 column TextView object? 

----

Yes, if you mean a 2 column TableView object (not TextView). Just do something like:

    
 - (int)numberOfRowsInTableView:(NSTableView *)tableView
 {
     return [arrayA count]; // I'll assume both arrays have the same count.
 }
 
 - (id)tableView:(NSTableView *)tableView
       objectValueForTableColumn:(NSTableColumn *)tableColumn
       row:(int)row
 {
     id value = nil;
 
     if (tableColumn identifier] isEqualToString:@"first column"])
     {
         value = [arrayA objectAtIndex:row];
     }
     else
     {
         value = [arrayB objectAtIndex:row];
     }
  
     return value;
 }


A better way to do it though, is have one array containing [[NSDictionary objects and just do:

    
 - (id)tableView:(NSTableView *)tableView
       objectValueForTableColumn:(NSTableColumn *)tableColumn
       row:(int)row
 {
     //column identifiers should be the same as the dictionary keys you wish to display
     //you can also store metadata in here as well (file paths, alias data, attributed vs. non-attributed representations...)
     return tableArray objectAtIndex:row] objectForKey:[tableColumn identifier; 
 }

----
How would you get the cell selection colour to change? I'd really like to see it turn into a gradient! I'm guessing you'd use
    
 - (void)tableView:(NSTableView *)aTableView willDisplayCell:(id)aCell forTableColumn:(NSTableColumn *)aTableColumn row:(int)rowIndex

Whats the stuff you change to set the cell selection colour to a gradient?
Cheers
Louis
----

How would you make your cell draw 2 lines of text, the top line bigger than the other(i.e. Acquisition's download list)?

thx --B.

*Just return an attributed string with the top line bigger from the     objectValueForTableColumn... method*

Or just create a custom NSCell as David does in Acquisition.

----
Hey, could anyone tell me how to implement simple action sending using the return key?
I try to have my table view easily navigated using only the keyboard. Changing selections are okay, but how can I make the table perform a method if the return key was pressed?
Thx,
Ben

*Subclass NSTableView, override -keyDown:.*

yeah, I figured that, but I couldn't get it to work, so I thought someone might point me towards a sample or something.
I also find it strange that NSResponder has methods for deleteForward: but nothing for return: or enter: or am I wrong there?

*
Add a button, and choose its key equivalent to be the 'return key'. Add the desired action of the button in your code and link it in Interface Builder. If you don't want to see the button just use the 'hidden' option or put it away from the visible user interface. Hope this helps. 
Marc
*

Another way to figure this out is to override keyDown and then check if the event's keyCode is 36. --gabe

----

There's an     insertNewline: isn't there?

----

There is. I don't konw how I missed that one. Thanks, it works now :)

----

How do you capture the keystrokes of text cells that are currently being edited? The TableView doesn't seem to be the key view once you start editing cells... what is the new key view? How do you subclass / access it? Thanks - Charlie

*
Answered my own question (eventually), look up the FieldEditor to learn more about this. But basically, you implement the     - (id)windowWillReturnFieldEditor:(NSWindow *)sender toObject:(id)anObject method in your view window's delegate. In this method you return a subclassed NSTextView that contains your keyDown: code. However, I'm having problems using reloadData while editing within this subclassed NSTextView, when I call it on a NSTableView while editing inside the FieldEditor the NSTableView does not call it's delegate method     - (id)tableView:(NSTableView *)aTableView objectValueForTableColumn:(NSTableColumn *)aTableColumn row:(int) rowIndex. It is bizzarre. Any ideas?
*

----

I need to allow rearranging items in a table view by dragging and dropping them (placing them in an arbitrary order).  Are there any examples of code for this?  Thanks.

----

A comprehensive example of dragging rows around should be in your     /Developer/Examples/AppKit/DragNDropOutlineView folder.

----

There is a simple example for NSTableView drag and drop in http://www.xboy360.com/. It can be download.  lepton

----

The example of lepton will not work if you do not have the Japanese language on your system. This is due to the japanese nib that is unable to load when you don't have the japanese language installed (you can chose to install or not several languages during the install if OS X). If you are developing in Cocoa for Mac OS X then you should at least have all of the default languages installed, otherwise you are only pretending ;)

----
Recall that the datasource you are using must be retained. I ran into trouble with the NSTableView crashing in my app when it was calling objectValueForTableColumn() with an objc_msgSend error. Turns out that the NSArray that I was using as my datasource was created with a convenience method and was not being retained. The table would show my data momentarily and then crash. Adding proper memory management [myArray retain]; solved this hard to track down issue.

----
This is a little misleading. An array can't be a dataSource for a table without help - it doesn't implement the dataSource methods. You could add them via a category, but this is inadvisable - it breaks Model-View-Controller (MVC) design principles. Typically, the dataSource for a table is implemented by a controller. The controller owns (retains) the table, so the situation where the dataSource can go away leaving the table behind shouldn't normally arise. If the model is an array, the controller would typically retain a reference to that as well. Because the controller is in charge, it should be doing the appropriate memory management. The table only has a weak reference to the dataSource for this reason - it expects a controller to be managing that. --GrahamCox

----
An example of drag and drop with core data and groups:
http://web.mac.com/agerson/Site/CoreDataDNDWithGroups.html


----
The previous examples of enabling editing a particular tableview cell after inserting a new row don't work with Core Data as the data source.  I finally ran across someone who had the right answer.  The source for this is here http://www.friday.com/bbum/2006/05/18/core-data-array-controller-edit-on-insert/
Here is the snippet straight from his page:

    
 - (IBAction) insertNewWeight: sender;
 {
 	NSManagedObject *insertedObject = [weightEntryArrayController newObject];
 	unsigned newRow = [weightinator rawWeightCount];
 	[weightEntryArrayController insertObject: insertedObject atArrangedObjectIndex: newRow];
 	[weightEntryTableView editColumn: 0 row: newRow withEvent: nil select: YES];
 	[insertedObject release];
 }


--
Evan

