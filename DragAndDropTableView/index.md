---
layout: page
title: DragAndDropTableView
---

This page contains notes about the methods you need to implement to use DragAndDrop in an NSTableView.

Please note that all information contained herein, while applicable to NSOutlineview in principle, is often implemented with its own methods.  Specifically, note the NSOutlineViewDataSource protocol defines its own, equivalent drag and drop methods which function equivalently to those listed bel ow. http://goo.gl/OeSCu
----
**Dragging to the tableview**

**First** you need to send <code>registerForDraggedTypes:(NSArray *)types</code> to your tableView, with the types that you want it to be able to accept. Examples are NSStringPboardType, NSFilenamesPboardType, etc...

Apple's Docs on this method (NSTableView inherits it from NSView, that's where you'll find this description):

*Registers pboardTypes as the pasteboard types that the receiver will accept as the destination of an image-dragging session.

Registering an NSView for dragged types automatically makes it a candidate destination object for a dragging session. As such, it must properly implement some or all of the NSDraggingDestination protocol methods. As a convenience, NSView provides default implementations of these methods.*

**Next** you need to implement some methods to accept the drag and incorporate the data. The tableview already handles the NSDraggingDestination protocol, so don't rewrite those - you need to write these methods (in the **datasource** object of your tableview)
    
- (NSDragOperation)tableView:(NSTableView*)tv 
	validateDrop:(id <NSDraggingInfo>)info 
	proposedRow:(int)row 
	proposedDropOperation:(NSTableViewDropOperation)op;
	// This method is used by NSTableView to determine a valid drop target. 
	// Based on the mouse position, the table view will suggest a proposed drop location.  
	//This method must return a value that indicates which dragging 
	// operation the data source will perform.  
	// The data source may "re-target" a drop if desired by calling 
        // setDropRow:dropOperation: and returning something other than 
        // NSDragOperationNone.  
	// One may choose to re-target for various reasons (eg. for better visual 
        // feedback when inserting into a sorted position).

- (BOOL)tableView:(NSTableView*)tv 
	acceptDrop:(id <NSDraggingInfo>)info 
	row:(int)row 
	dropOperation:(NSTableViewDropOperation)op;
	// This method is called when the mouse is released over a table view
	// that previously decided to allow a drop via the validateDrop method.
	//  The data source should incorporate the data from the dragging pasteboard at this time.


----
**Dragging from the tableview**

You need to implement this method:
    
- (BOOL)tableView:(NSTableView *)tv 
	writeRows:(NSArray*)rows 
	toPasteboard:(NSPasteboard*)pboard;
	// This method is called after it has been determined that
	// a drag should begin, but before the drag has been started.  
	// To refuse the drag, return NO.  To start a drag, return YES 
	// and place the drag data onto the pasteboard (data, owner, etc...).  
	// The drag image and other drag related information will be set up and 
	// provided by the table view once this call returns with YES.  
	// The rows array is the list of row numbers that will be participating in the drag.


You might also want to ** subclass ** NSTableView, in order to override the default behavior, which is to allow any kind of drag to within your application, and no drags to outside your application. In your subclass you need to implement the following method in the ** datasource ** object of your tablview.
    
- (NSDragOperation)draggingSourceOperationMaskForLocal:(BOOL)isLocal {
	// Return one of the following:
	// NSDragOperation{Copy, Link, Generic, Private, Move,
	//                 Delete, Every, None}
	if (isLocal)
		return what you want to happen if it's coming from your app;
	else
		return what you want to happen if it isn't;
}

This is the only required method in the NSDraggingSource informal protocol (the constants are explained in the documentation for NSDraggingInfo)  Changing this method is required if you want to allow drags to other applications, because the default implementation of NSTableView returns <code>NSDragOperationNone</code> for isLocal = NO.

**Note:** the <code>isLocal</code> argument doesn't tell you if the drag is coming from the same tableview! So, if you want <code>NSDragOperationNone</code> when it is from the same tableview but <code>NSDragOperationCopy || NSDragOperationLink </code> when it is from a different one, you have to return <code>NSDragOperationNone || NSDragOperationCopy || NSDragOperationLink</code> here if <code>isLocal</code> is YES, and sort it out later.

Another method you can implement in your subclass to provide a custom image is the following:

    
- (NSImage*)dragImageForRows:(NSArray*)dragRows
                       event:(NSEvent*)dragEvent
             dragImageOffset:(NSPointPointer)dragImageOffset {

    return some NSImage;
}


A suggestion for this one, since you know the rows, if you're returning an NSStringPboardType in the datasource's <code> tableView:objectValueforTableColumn:row:</code> method, you can save yourself the mess of duplicating code by creating a new NSPasteboard (using <code>- pasteboardWithUniqueName </code>) and sending <code> tableView:writeRows:toPasteboard:</code> to <code>[self dataSource]</code>, getting the string out of that Pasteboard and drawing it into the image.

If you're not too familiar with quartz drawing, luckily this is not too bad. You  just get an NSAttributedString from the NSString the pasteboard gave you, make a  new NSImage (alloc, init, autorelease) and then do this dance: 
    
[image lockFocus];
[string drawAtPoint:NSZeroPoint];
[image unlockFocus];

you can also set the <code>dragImageOffset</code> argument to change where the image is attached to the pointer. The caller of this method passed you an NSPointPointer, which means you should change the value of <code>*dragImageOffset</code>, not create a new NSPoint (on the stack) and try to pass it out by re-setting the pointer. That won't work...

Here are Apple's docs on this method from <code> NSTableView.h: 

</code>
    // This method computes and returns an image to use for dragging.  Override this to return a custom image.  'dragRows' represents the rows participating in the drag.  'dragEvent' is a reference to the mouse down event that began the drag.  'dragImageOffset' is an in/out parameter.  This method will be called with dragImageOffset set to NSZeroPoint, but it can be modified to re-position the returned image.  A dragImageOffset of NSZeroPoint will cause the image to be centered under the mouse.

----
You might want to check out http://developer.apple.com/releasenotes/Cocoa/AppKitOlderNotes.html
which has a section on Drag And Drop Support in NSTableView and NSOutlineView. I couldn't find any other Apple documents on the subject.

----
**Comments & Discussion:**
It looks like <code>draggingSourceOperationMaskForLocal:</code> is already implemented in the standard NSTableView, I have a running version that uses just the NSTableDataSource methods to implement rearranging the items of a table within itself.

The missing function for me to get the whole thing working was </code> registerForDraggedTypes:</code> which is a method of NSView and needs to be called on the table to enable dropping onto the table.

-- HaRald

HaRald, thanks for pointing this out! I didn't notice that. A quick look with FScript tells me that a straight NSTableView alloc, init gives me a tableview that responds to <code>draggingSourceOperationMaskForLocal:</code> with NSDragOperationAll for isLocal = YES, and NSDragOperationNone for isLocal = NO.

-- MichaelMcCracken

----

Well I will give this a shot, as it has been said the documentation is there but it spans a couple of issues.

Look at MichaelMcCracken's descriptions above for more info on the functions

    
    NSString * const PBType = @"MyType" 

    [pageTable registerForDraggedTypes:[NSArray arrayWithObject:PBType]]; 


Call this anywhere (awakeFromNib, windowControllerDidLoadNib, ...). This actually enables you to receive Drags of the declared type(s), there are a couple of standard types declared, but using a custom type protects you from people dragging weird things into your app. A custom type acts as a filter but more specific than draggingSourceOperationMaskForLocal:.

This is part of working code, the following assumption are made and valid,
this table has only one column and only one item can be selected and the items in the table are all unique strings, i did replace some variable names.

Pasteboards 101: There is a variety of boards used for different purposed (Cut and Paste, Dragging, ...) each board can be supplied by one owner at a time, if someone else grabs that board the current content is invalidated. Content can be put onto the pasteboard immediately or held by the owner for lazy evaluation. It then has to be provided whenever someone else actually needs it, or application of the owner quits. The following example provides the data immediately and also only provides one type of data. 

    

@implementation DragTableDataSource (NSTableDataSource)

- (BOOL)tableView:(NSTableView *)tableView
        writeRows:(NSArray*)rows
     toPasteboard:(NSPasteboard*)pboard {
     
    NSNumber *row = [rows objectAtIndex:0];
    NSString *string = [dataArray objectAtIndex:[row intValue]];
    
    // Tell the pasteboard what type it could contain
    // This clears the pasteboard
    // owner:nil means that data is immediately available and 
    // the we don't need to keep owner instances around because
    // they might be called at a later time
    [pboard declareTypes:[NSArray arrayWithObject:PBType] owner:nil];
    
    // Add the data of to the pasteboard
    [pboard setString:string forType:PBType];

    return YES;

}


 - (NSDragOperation)tableView:(NSTableView*)tv 
                 validateDrop:(id <NSDraggingInfo>)info 
                  proposedRow:(int)row
        proposedDropOperation:(NSTableViewDropOperation)op {
    
    // Before the drop gets executed we get a chance to change the
    // actual place where the drop is going to happen
    // In this case i dont want to drop onto the row but above the row  
    if (op == NSTableViewDropOn) {
        [tv setDropRow:(row+1) dropOperation:NSTableViewDropAbove];
    }
    
    return NSDragOperationGeneric;
 }


- (BOOL)tableView:(NSTableView*)tv
       acceptDrop:(id <NSDraggingInfo>)info
              row:(int)row 
    dropOperation:(NSTableViewDropOperation)op {
    
	// get the pasteboard where all out data is stored
    NSPasteboard *pboard = [info draggingPasteboard];
    
    // We are only accepting drag operations of type PBType
    // so this is save
    NSString *title = [pboard stringForType:PBType];
    NSString *selectedTitle = nil;
    int oldRow = [pageArray indexOfObject:title];

	// Remember what was selected
    if ([tv selectedRow] > -1 ) {
        selectedTitle = [pageArray objectAtIndex:[tv selectedRow]];
    }
    
    // perform the swap
    [self moveTitle:title toRow:row];

	// Correct the selection
    if (selectedTitle != nil) {
        [tv selectRow:[pageArray indexOfObject:selectedTitle]
 byExtendingSelection:NO];
    }
    
    // Update the table
    [tv reloadData];
    
    
    return YES;
}


@end



I hope you don't mind my barging in Michael, I had this sitting around ... 

-- HaRald

----
Not at all. Just one note: there are cases when <code>[NSPasteboard pasteboardWithName:NSDragPboard]</code> doesn't work as expected (it isn't guaranteed to give the right pasteboard in a drag from another application) and so you should use <code>[info draggingPasteboard]</code> instead.

Apple says this:
"there is NO guarantee that this will be the pasteboard used in a cross-process drag. Thus, to guarantee getting the correct pasteboard, your code should use sender.draggingPasteboard() or [sender draggingPasteboard]." They say sender instead of info because they're talking about more general NSDraggingDestination methods, not NSTableView specific methods.

Rewrote accordingly -- HaRald

----
Stone's (AndrewStone) Cocoamotion page at http://www.stone.com/The_Cocoa_Files/Cocoamotion.html has a bit of code they use for drag and drop reordering. It seems to work after you realize you need to add a retain/release for the row object ([array objectAtIndex:rowToMove]) to the tableView:didDepositRow:at: method. 

-- QetiPadgu
----
If you're timid about bindings ArrayControllers, etc., and would rather do this programmatically, I would recommend the AndrewStone method that QetiPadgu pointed out. (BTW, thank you for posting it. It was a big help!)

-- JasonTerhorst
----
So it seems no one has really gotten file dragging from an NSTableView to the Finder to work properly? I need to drag rows from my TableView to the Finder and have files created there. Promise doesn't seem to work... anyone?

garrett

*What exactly are you doing? You have to create the files, Finder can't.*

maybe this will help: check out the docs on lazy drag evaluation, basically you tell the pasteboard you have certain types of data, and if anyone wants it ask **self** then when it recieves you get a notification asking for the data, and in that routine you could create the files and hand back nil or something....

---

I just got it working. The key was to return the right constant (NSDragOperationCopy in my case) in draggingSourceOperationMaskForLocal:

Daniel

----
Since the introduction of bindings we don't need a data source any more. How are drag and drop handled in NSTableView with an NSArrayController? ... Nevermind, Malcolm Crawford's bookmarks example from http://homepage.mac.com/mmalc/CocoaExamples/controllers.html shows how (and you still need a data source).

*you never really needed a separate data source object - you could always have just added the NSTableDataSource protocol methods as a category on NSArray.*

----

CAREFUL with this assumption. There are MANY circumstances where developers (such as yours truly) get caught up into trying to FORCE everything to fit into the Bindings mechanism. There is still a GREAT need for datasource/delegates. Mixing the two is perfectly normal and you shouldn't feel like you're doing something wrong here. ;-)

**I think you mean NSArrayController...I usually end up subclassing it and designating it as a data source, a delegate, and a bindings target. It might seem like too much, but I think of it as a class that manages the table, and has array management built in. --JediKnil**

----
Does ANYONE have an example of how to drag from an NSTableView to the desktop to create a file? I *know* it can be done since I've seen applications do it. There's no lack of discussions on the problems people have had trying to implement this on the web, but with only bits and pieces of code that don't provide a working example. The total lack of documentation from Apple on this is simply pathetic. 

Please note that I'd like this to be compatible w/10.2 & 10.3, so I can't use the functions provided for this (finally!) in 10.4 -Seb

----

I've struggled (as many, many others did) with this exact situation also (it *is* badly documented, and partially broken up to 10.3.9), and through all the noise, I did find some useful code on a mailing list (see http://cocoa.mamasam.com/COCOADEV/2003/05/1/63270.php ) which provides a 'bare-bones' implementation of a working tableview-to-desktop-file-creation-drag, which I will copy here for completeness:
    
 @implementation MyTableView

 - (NSDragOperation)draggingSourceOperationMaskForLocal:(BOOL)isLocal {

 �� � if (isLocal) return NSDragOperationNone;
 �� � else return NSDragOperationCopy;
 }

 - (void)mouseDown:(NSEvent *)theEvent
 {
 �� � NSPoint dragPosition;
 �� � NSRect imageLocation;
 �� � int row;


 �� � dragPosition = [self convertPoint:[theEvent locationInWindow] 
 fromView:nil];

 �� � row = [self rowAtPoint:dragPosition];

 �� � if ( (row != -1) && (row < self delegate] nRows]) ) {

 �� �[self selectRow:row byExtendingSelection:NO];
 �� �
 �� �dragPosition.x -= 16;
 �� �dragPosition.y -= 16;
 �� �imageLocation.origin = dragPosition;
 �� �imageLocation.size = [[NSMakeSize( 32, 32 );

 �� �printf ( "mouseDown: called (x: %f, y:%f, row:%d)\n", dragPosition.x, 
 dragPosition.y, row );

 �� �[self dragPromisedFilesOfTypes:[NSArray arrayWithObject:@""]
 �� � � �fromRect:imageLocation source:self slideBack:YES event:theEvent];
 �� � }
 }

 - (NSArray *)namesOfPromisedFilesDroppedAtDestination:(NSURL *)url
 {
 �� � printf( "Dropped to: %s\n", url path] cString] );

 �� � return nil;
 }

 @end

The original discussion begins at http://cocoa.mamasam.com/COCOADEV/2003/05/1/63257.php which also explains why some stuff doesn't work as expected with drags of tableviews.

*That code sorta works, but isn't really appropriate unless you have a static (non-selectable) table; as discussed later in that same thread, it assumes any click is a drag, and messes with the actions and makes the tableview react in a strange manner when used as a normal (selectable) tableview.

It doesn't appear there's any real way to accomplish this, lest Apple finally comes up with clear documentation on implementing this (contrary to others' claims that it's well documented)*

http://www.cocoabuilder.com/archive/message/cocoa/2003/8/20/89717 implements a slightly better version of the code above, but still inhibits double-click on rows. 

File creation dragging from a [[NSTableView is definitively broken (at least on <=10.3.9). Others have tried and failed, and I've just joined that club. -Seb

----

