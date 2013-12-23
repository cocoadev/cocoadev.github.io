---
layout: page
title: NSTableView
---

http://homepage.mac.com/mkino2/cocoaProg/AppKit/NSTableView/image/stripe.jpg
An AppKit class used for managing NSArray data.

*Although it is most certainly almost always best to use NSArray to supply a NSTableView with data, there's nothing in the API that dictates this. Just wanted to make that clear.* http://goo.gl/OeSCu

----
http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/Classes/NSTableView_Class/index.html#//apple_ref/doc/uid/TP40004125

[Topic]

----

(Not adding a description, as I do not feel qualified yet :)

Note that -reloadData has an unexpected side-effect: it forces the current editing to stop and the changes to be verified. As far as I know, this is the only way to do this (and I searched for a while before trying it out of desperation). So if you wish to keep what the user has done, just call this; if not, you'll need to set up a delegate to reject the changes, or get the data source to do it (whichever makes more sense in your design).

Just to save others the effort...

-- KritTer

----

Also, if anyone is trying to get row rearranging by dragging to work, you could to worse than scour the source for FoundationCollectionsPalette; I have a working example (I hope!) -- KritTer

There's also Andrew Stone's excellent SDTableView, at http://www.stone.com/dev/SDTableView/ReordingTableView.html

--JackNutting

Easy way out -- use a data source: http://homepage.mac.com/mmalc/CocoaExamples/Bookmarks.zip Use the DNDArrayController class for the content binding and the data source (and the delegate, if you want to...but I don't think this is required) -- JediKnil

----

More information that may prove useful: as someone on the CocoaDev mailing list has found, no matter how many times you click on the header of a table, it won't become FirstResponder. This may surprise you, but you can always add code to make it so.

-- Kritter

----

I was also surprised to learn that clicking & double-clicking a table column header sends the action & doubleAction respectively to the table's delegate.  So, in your action methods be sure to send clickedRow to your table, check if it's -1, and behave appropriately.

-- StevenFrank

----

I've started NSTableViewTutorial to start a single article for collecting basic table view information, since this page seems to be a collection of random musings.

-- MikeTrent

----

I was experiencing problems with an NSTableView updating and turned to the web for answers. Others had found that -noteNumberOfRowsChanged did not cause the table to fully update and display new data. Suggested remedies to this included calling -reloadData, -display, -setNeedsDisplay:YES and -displayIfNeeded (to both the view and the window) or combinations of these. Some apparently had success with this. None of these worked for me. A previous project created under pre-Jaguar dev tools worked well. When recompiled under post-Jaguar tools it still worked. So, the only difference I could find was that in the old project I had created a simple NSObject derived class that handled the table. In my newer effort I was creating a multi-document application so the class handling the table was derived from NSDocument. Tonight I tested the theory that this was the crucial difference by creating a very simple project that displays a window with a single column table, an add button and a remove button. The column is intended to display a row number and the buttons add and remove rows from the table. Simple. It works perfectly for the NSObject derived class using only a call to -noteNumberOfRowsChanged. It doesn't work at all for the NSDocument derived class.

Why?

Clicking on a row that has not been redrawn and appears not to be there does force a UI update and reveals the row. Clicking on a removed row leaves the image of the row intact but nothing happens (since it isn't really there).

-- LeeCalloway

----

Several things I have noticed with NSTableView

If you archive a NSTableView and copy it to the dragging clipboard and then read it off the clipboard, then when you scroll the clipView blurs up and becomes an illegible mess of a scribble. I was told by respectable sources this was an AppKit bug.

The other issue is if you update your project to Jaguar's project builder, you can no longer archive tableViews with a non-keyed archiver for some reason.

--GormanChristian

----

Apple has acknowledged a bug with NSTableView on MacOSX 10.2, as well as a temporary workaround. See http://developer.apple.com/qa/qa2001/qa1220.html

----

Problems with NSTableView, and solutions:

*It does not show a focus rectangle when it has keyboard focus. If there are no rows in the table, there's no indication that it has focus. *Solution:* See FocusRingAroundTableView. *Solution:* Use Panther.

*It does not implement full keyboard control (Home, Cmd-Up, Esc, Delete, etc.). *Solution:* Subclass NSTableView, interpret key-down events as selectors, and override the corresponding methods to do what you want. See http://cocoa.mamasam.com/COCOADEV/2002/07/2/40890.php.

*Ending editing via Return does not leave the just-edited item selected, but changes the selection. *Solution:* Convert the return event into an illegal text motion event. See http://cocoa.mamasam.com/COCOADEV/2002/01/2/22208.php.

Another solution to the same problem is given here: OneTimeEditInTableView.

*Can not contain NSProgressBar or NSColorWell. *Solution:* There is a framework called "TDBCTableCells" by Tenoch De La Cruz Baltazar. $15 http://www.xoono.net/bricks.html

*Can not vary cell-type on a row-by-row basis. Example, if a cell should be an image cell or text cell based on the record's data, this thing cannot be done. *Solution:* A custom cell type that will evaluate the data itself. *Solution:* Override the NSTableColumn's dataCellForRow: method.


-- DustinVoss

----

http://flangy.com/dev/osx/tableviewdemo/

Just some sample NSTableView code I whipped up, though there's probably better stuff out there. Sets custom cells and has an editable data source.

-- AdamVandenberg

----

Subviews in TableView Rows  [http://www.stepwise.com/Articles/Technical/2003-12-20.01.html]

An article with code that illustrates some additional features of Table Views.  Describes how to place NSProgressIndicator and NSImageWell objects in a table.

----

Copying from a TableView in Panther+:

    
- (void)copy:(id)sender
{
    NSPasteboard *pb = [NSPasteboard generalPasteboard];
    NSString *string = @"";
     
    switch ([self numberOfSelectedRows])
    {
        case 0:
            return;
        
        default:
        {
            id selection = [self selectedRowIndexes];
            int index = [selection firstIndex]; 
            
            
            do
            {
                string = [string stringByAppendingFormat: @"%@\n", 
                    self dataSource] 
                        tableView: self
                        objectValueForTableColumn: 
                            [[self tableColumns] objectAtIndex: 1]
                            // 1 being the column of data I want to copy
                        row: index;
                        
            } while ((index = [selection indexGreaterThanIndex: index])
                     != NSNotFound);
        }
    }
       
    [pb 
        declareTypes: [NSArray arrayWithObject:NSStringPboardType] 
        owner:nil];
        
    [pb setString:string
         forType: NSStringPboardType];
}

-- Jeffrey Drake (wackysteak at your local mac.com)

----

How would one create iTunes-like behavior, where a fast double click opens, but a slow double-click begins edition? Would you have to override pretty much all NSTableView's click/drag handling guts?

----

you could maintain your own click timer and your own double click interval (longer than the system double click speed) and if you get a 2nd single click within that interval, start editing.

http://www.cocoabuilder.com/archive/message/cocoa/2003/10/22/90323 shows how to get the system dbl click interval. the rest of the thread is pretty entertaining too...

----

iTunes doesn't actually make such a distinction. There is one action assigned to a double-click, and one action assigned to clicking on an already-selected row. What you refer to as a 'slow double-click' is in fact two single clicks with different action. Try selecting an item with the arrow keys and then single-clicking on an item; you'll get the editing box. In other words, the regular single and double click methods should work fine for this purpose.

----

I'm somewhat amused that in the screenshot above, the indices are not, uh, the indices. :-)  I understand that those are the indices as far as humans are concerned, but we ain't humans 'round these parts, we're programmers!  On an instructive programming site, it's more user-friendly to use the underlying 0-indexing.

Basically I'm just musing on this because I think it's an interesting example of "user-friendly" varying with context.

-KenFerry

----
Newbie NSTableView question:  I want to create a version of NSTableView whereby I can individually select cells - by this I mean not select whole rows, so that if my NSTableView consisted of 4 rows and 4 columns the user could select/highlight the center 4 of 16 cells (the intersection of the 2 middle columns and the 2 middle rows).  Is it possible to do this?  How would one go about getting this behavior?  The reason I want to do this is I want some spreadsheet like capability for my users, like being able to fill right.  But I don't want to go to a NSMatrix necessarily as I want all the other features of NSTableView, like the scrolling, user drag-reorderable columns, etc.

*Sounds like you might be better off putting an NSMatrix in an NSScrollView and adding the drag-reorderable columns yourself... that may well be less work.*

----

**Question (Aug 5, 2005):** I'm having a hell of a time with a table view that allows "Any" as a first-row selection or multiple selections of any other row. In other words, I have a table of "Categories" where the first row is "Any Category" (like iTunes' artist/album tables). I'm using the data source approach, versus bindings (to insert the "Any" row). I'm using NSTableView's delegate method     -tableView:shouldSelectRow:. Because I read somewhere that it's "better" to use index sets, below is the code I'm using:

    
- (BOOL)tableView:(NSTableView *)aTableView shouldSelectRow:(int)rowIndex
{
	if (rowIndex == 0) // Row 0 is the "Any" row
	{
		// Selecting 'any', so deselect the rest
		NSIndexSet * selSet = [NSIndexSet indexSetWithIndex:0];
		[aTableView selectRowIndexes:selSet byExtendingSelection:NO];
	} else {
		// Selecting specific item, so deselect 'any'
		NSMutableIndexSet * selSet = aTableView selectedRowIndexes] mutableCopy];
		[selSet removeIndex:0];
		[aTableView selectRowIndexes:selSet byExtendingSelection:NO];
		[selSet release];
	}
	
	return YES;
}


With this code, selecting the first row (even using Cmd to try to extend the selection to "any") always deselects the first row correctly. This is fine. The problem is when you have "Any" selected (as the only selected row) and you use Cmd to extend the selection to any other row. It *should* deselect the first row. Instead, it keeps the first row selected and extends the selection to the new row. BUT, extending the selection to a THIRD selected row does remove the first row. I'm puzzled by this. Truly. :-) I suspect that because the first non-0 row isn't selected yet, it's not tripping the correct code. But that's why I have it checking which rowIndex is being requested. If it's anything other than the first row, (whether that new row is selected yet or not) it should trigger row 0's removal. Shouldn't it??

----

Hi,

Drag-reordering of an [[NSTableView is giving me a major headache at the moment. The only code I've found that comes close to helping is the Bookmarks example (http://homepage.mac.com/mmalc/CocoaExamples/Bookmarks.zip), but when I implement the DNDArrayController subclass, although drag and drop works, the dropped record is immediately deleted. 

I'm trying to maintain a simple list of clients, with the user able to drag-reorder them. Not after copying or dragging from finder, or anything fancy like that; just reordering existing contents. Surely this shouldn't be so difficult....? Could anyone point me in the right direction?

Thanks in advance,
Chris

*More info, please. You tell us basically nothing. What do you mean the 'record is deleted'? You mean the object is removed from the array controller? This seems ... odd. Also, how are you 'implementing' it? What have you changed from the example (it works as advertised for me).*

Hi, I'm using an NSTableView with Bindings to an NSArrayController subclass. This subsclass is also the delegate/datasource of the NSTableView. I've used the code from the Bookmarks example (and tried several other code fragments) Bookmarks is the only one to come close to working. I've used the code from the DNDArrayController subclass as is, except for changing the type of stored data. The actual drag/drop works fine; if I remove the     [self removeObjectAtArrangedObjectIndex:removeIndex]; line from the     -(void) moveObjectsInArrangedObjectsFromIndexes:(NSIndexSet*)indexSet toIndex:(unsigned int)insertIndex method, the dragged item is copied and placed above the 'drop' row, but (correctly) the old (drag-row) is not deleted. If I leave the removeObject code in, both the drag-row and the drop-row are deleted. The Bookmarks example works fine for me, so I'm not sure why it doesn't work correctly in my app.

As I'm writing this I'm beginning to wonder if it's because I'm using a binding, and whats actually being deleted is the reference to the record in the NSArrayController. That is, the tableview is showing two references to the same object, and the removeObject line deletes the referenced object, not the reference. Would (does!?) this make sense? If so, how would I go about preventing it? I can post all the code if it would make it easier.

Thanks,
Chris
----
I've been playing with that code a bit, and probably your item is getting released before you add it back in or something. Here's a version that seems to work better (for me). --JediKnil
    
- (void)moveObjectsInArrangedObjectsFromIndexes:(NSIndexSet *)indexSet toIndex:(unsigned int)insertIndex
{
    NSArray		*objects = self arrangedObjects] copy];
	int			index = [indexSet lastIndex];
	
    int			aboveInsertIndexCount = 0;
    id			object;
    int			removeIndex;
	
    while ([[NSNotFound != index)
	{
		if (index >= insertIndex) {
			removeIndex = index + aboveInsertIndexCount;
			aboveInsertIndexCount += 1;
		}
		else
		{
			removeIndex = index;
			insertIndex -= 1;
		}
		
		object = [objects objectAtIndex:index];
		[self removeObjectAtArrangedObjectIndex:removeIndex];
		[self insertObject:object atArrangedObjectIndex:insertIndex];
		
		index = [indexSet indexLessThanIndex:index];
    }
	
	[objects release];
}



Thanks JediKnil, unfortunately the problem still exists. It's virtually guaranteed to be something trivial I'm doing wrong, I've looked elsewhere through the code for instances where the record might be released, but can't see anything. All my code, by the way, is in a ClientsArrayController.m subclass of NSArrayController, which is set as the datasource and delegate of my NSTableView.

Followup: I've just written a test application from scratch that allows rearranging objects in an NSTableView fine. However, when I plug exactly the same code into my main app, the record still disappears after it has been dragged. The only difference between the two is that my main app uses CoreData whilst the test app just used Bindings and my own model class file. Would using CoreData be the problem? I'm guessing it's still somethiing to do with deleting the object itself, not the reference to it, in which case does anyone know how to copy an NSManagedObject, reinsert it in the array controller so that i could then delete the old object?

----

If you are trying to reorder a Managed Object in the table, do something like this in your NSArrayController subclass:

    

-(void) moveObjectsInArrangedObjectsFromIndexes:(NSIndexSet*)indexSet
										toIndex:(unsigned int)insertIndex
{
	
    NSArray		*objects = [self arrangedObjects];
	int			index = [indexSet lastIndex];
	
    int			aboveInsertIndexCount = 0;
    id			object;
    int			removeIndex;
	
    while (NSNotFound != index)
	{
		if (index >= insertIndex) {
			removeIndex = index + aboveInsertIndexCount;
			aboveInsertIndexCount += 1;
		}
		else
		{
			removeIndex = index;
			insertIndex -= 1;
		}
		object = objects objectAtIndex:removeIndex] retain];
		[self removeObjectAtArrangedObjectIndex:removeIndex];
		
		[[self managedObjectContext] processPendingChanges];

		[self insertObject:object atArrangedObjectIndex:insertIndex];
		
		index = [indexSet indexLessThanIndex:index];
    }
}



Note the "processPendingChanges" line.  That's likely what's messin' ya up.  Core Data delays all actions until the next event loop for speed.  If you depend on the "answer" then you need to force the change. --codepoet

Thanks codepoet! That's got it working - always the smallest of changes causing the biggest of problems! The drag reordering now works, although it has thrown up another error whereby dragged entities lose their connection to other entities. I.e. if Entity A contains 3 Entity B's, dragging Entity A removes the link to the Entity B's. I'm thinking this is to do with the 'delete object, insert  copy' method so I'll look into it a bit more. Thanks again for everyone's help! -- Chris

----

Although it's for an outline view & tree controller, you might look at my sample code for [[DragAndDropOutlineEdit which shows dragging managed objects along with their relationships. See http://homepage.mac.com/matthol2/cocoa/page2/page2.html for the project files; the same site has a short blog entry on this subject. Generally I also have to delete/insert -- the trick is to package up on the clipboard MO URIs so that drops within the same view/context can take care of the deletion/insertion and reset the relationships (e.g., parent to child). And when the relationship is recursive there's a bit more work. -- Matt

----

Does anyone know how to make it so that a mouse down on a table header does not deselect the selection, i'm trying to implement different orderings and this is totally messing it up. 

----
Maybe your delegate could implement     tableView:shouldSelectTableColumn:, grab the selectedRow (or indexes), figure out what's really selected (since itmay get re-ordered) and reset the selection in     tableView:didClickTableColumn: and/or     tableView:didDragTableColumn:...or something of that nature?

----

I'm trying to use NSCell-setAttributedString: in -tableView:willDisplayCell:forTableColumn:row: and it's not working.  What is the proper way to set a NSTableView sell to an NSAttributedString in this method?  I'd rather not return the string in -tableView:objectValueForTableColumn:row: because I'm using the actual object value for something else, I'd just like to have it drawn as an attributed string.

----
Nevermind, I have it fixed.  I guess I was getting it confused because the NSOutlineView data source works a little differently.

----
When using a gradient background for the selected row, you want your text to become white and have a bit of shadow (like mail.app). This little snippet shows how.

    
- (void)tableView:(NSTableView *)tableView willDisplayCell:(id)cell forTableColumn:(NSTableColumn *)tableColumn row:(int)row
{
	NSColor *fontColor;
	NSColor *shadowColor;
	if (tableView selectedRowIndexes] containsIndex:row] && ([tableView editedRow] != row))
	{
		
		fontColor = [[[NSColor whiteColor];
		shadowColor = [NSColor colorWithDeviceRed:(127.0/255.0) green:(140.0/255.0) blue:(160.0/255.0) alpha:1.0];
	}
	else
	{
		fontColor = [NSColor blackColor];
		shadowColor = nil;
	}
	[cell setTextColor:fontColor];
	NSShadow *shadow = [[NSShadow alloc] init];
	NSSize shadowOffset = { width: 1.0, height: -1.5};
	[shadow setShadowOffset:shadowOffset];
	[shadow setShadowColor:shadowColor];
	[shadow set];	
}


----
Is there any way to set things up so that double-clicking on a cell sends the message to a different object (i.e. not the cell)?  I thought setting the NSTableView<nowiki/>'s delegate would make the message be sent to the delegate, but that seems not to have worked.

*Set the target and doubleAction.*

----
I want to have an image in a column of each row of a table. I have looked around on NSImageCell and read something about subclassing NSCell to draw the image, but I just want an image in it's own column. I want a different image for each row. Any idea on how I might go about this?
-ACoolie.

----
In IB drag an NSImageCell from the Data palette (its the one that has a picture of a mountain on it), onto the row(s) of your table view. Then in your delegate method: - (id)tableView:(NSTableView *)aTableView objectValueForTableColumn:(NSTableColumn *)aTableColumn row:(int)rowIndex, return the proper image for the column.

-- Jacob

----
Is there a way to place several side-by-side buttons in a single cell? The closest example I can think of is the "My Rating" column in iTunes, where the five stars are sort of like five buttons. However, I'd need for the buttons to have text in them. Thanks gurus for any hints you may have! -- Theo

----

NSSegmentedCell is the way to do this. However, the rating widget in iTunes is implemented by NSLevelIndicatorCell.

----
I'm building a simple CoreData non-document application.  So far everything is working perfectly. I have a NSTableView bound to a NSArrayController, and as I add new rows, everything is displayed correctly. Also, I have an add button that is bound/link to the NSArrayController's add target.  The only thing I can't figure out is when the add button is hit, a new row is inserted into the NSTableView, and the new row is selected.  However, I would like to start editing the first column in the new row.  I thought I could use the delegate method tableViewSelectionDidChange: to see when a new row is added to the NSTableView.  Well that doesn't get fired if there are already rows in the NSTableView.  However, if the tableview is empty, the tableViewSelectionDidChange: fires.  So my question is how can I set the new row into editing mode.  I'm sure I missing a delegate method that notifies of newly inserted rows into a NSTableView, but I can't find it.  Suggestions?  John

----

OK, after more hair pulling with the CoreData and NSTableView, I finally figured something out.  First, in a CoreData app that uses a NSArrayController, the     tableViewSelectionDidChange notification will not fire on inserting a new row if there is a selected row.  Why I don't know.  Doing the same thing with only a NSArrayController and NSTableView (without the CoreData stuff), the notification fires on insert.

What I finally did is register an Observer to the NSArrayController's selection property. So now I get a notification everytime the selection changes in the NSArrayController. When I insert a new row, I get notified.  Then I check the row to see if a property of the CoreData entity object is null.  If it is, then I set the tableView to     editColumn:row:withEvent:select: to edit the first column. John

----

Is it possible to prevent NSTableView from autoselecting newly added rows?

----
To the two previous questions, it is probably better practice to just roll your own add: method to handle your desired behaviour rather than register for notifications. This way you know that your code is only being called when you are adding objects.

----
I would expect the tableViewSelectionIsChanging delegate to always fires before the tableViewSelectionDIdChange delegate. However, I've found that tableViewSelectionIsChanging only fires if the user selects rows with the mouse. For instance, if you use the Command-A shortcut to select rows, the tableViewSelectionIsChanging delegate doesn't fire (tho the tableViewSelectionDidChange fires as expected). I've found nothing in the documentation that suggests that things should work this way. Any thoughts? I'm developing on Tiger. Thanks!

----
That's just how it is, kids. Not the most consistent, useful implementation, to be sure.

----
To enable the detection of double clicks make sure the table columns are non editable. Then you can enable any controller to respond to double clicks with:

[aTableView setTarget:self];
[aTableView setDoubleAction:@selector(myTableDoubleClickAction:)];

----
I would like to add a new row to a table view and automatically enable editing for the row (just as if the user had double-clicked the row). How can I fake out a double-click on the new row ?

Alec

----
Alec, try something like this:
    
- (IBAction) add: (id)sender
{
    /* whatever else you need to do */
    
    [myTable selectRowIndexes: [NSIndexSet indexSetWithIndex: [tableContents count] - 1]
               byExtendingSelection: NO];
    [myTable editColumn: 0 
                                row: [tableContents count]-1 
                       withEvent: Nil 
                             select: YES]; // of course you can change which column you start with
}


Update:

Actually, I had the same question as Alec, which is what led me to this page. What I wrote above is what I did for a single ("older-style") controller which handles all the different tables, but working with bindings and having Array Controllers for each table, this is what I have now come up with:

(I'm working with PyObjC, so I translated this; forgive me if my Obj-C syntax is a bit off)

    

// Make your own subclass of NSArrayController with the
// following overridden methods.

- (void)awakeFromNib
{
    // register this instance to get notifications from itself
    [[NSNotificationCenter defaultCenter] addObserver: self 
                                             selector: @selector(objectAdded:) 
                                                 name: @"Object Added" 
                                               object: self]
}

- (void)add: (id)sender
{
    [super add: sender]
    NSNotification * note = [NSNotification 
                                notificationWithName: @"Object Added" 
                                              object: self]
    // The add method doesn't really take effect until this run loop ends,
    // (see NSArrayController docs) so the notification needs 
    // to wait to post. Thus, enqueue with NSPostWhenIdle
    [[NSNotificationQueue defaultQueue] enqueueNotification: note
                                               postingStyle: NSPostWhenIdle]
}

- (void)objectAdded: (NSNotification *)note
{
	// when the notification finally arrives, tell the table to edit
	[[self contentTable] editColumn:0 
                                    row:[self selectionIndex] 
                              withEvent:nil 
                                 select:YES]
}


That seems to work. Hopefully it's helpful, but I'd love to know if I'm doing something stupid or unnecessary.

