---
layout: page
title: IconAndTextInTableCell
---



StevenFrank asked me to summarize an answer I gave on MacOsxDev.

If you have an NSTableView, and you want to get items in it that contain text AND an  image, (like Finder's "columns view" you have to use NSBrowserCell as your dataCell for each column in the table that you'd like this feature.  You have to tell the NSBrowserCell that it is a leaf node so it doesn't draw the arrow at the right edge.

    
	// table is set in IB
	NSTableColumn col = table tableColumns] objectAtIndex: 0]; // Should this not be [[NSTableColumn *col and NSBrowserCell *cel?
	NSBrowserCell cell = [[NSBrowserCell alloc] init];
	[cell setLeaf: YES];
	[col setDataCell: cell]


Then in the table delegate's willDisplayCell method, you need to set the image on the datacell for each row.

    
- (void)tableView:(NSTableView *)view
    willDisplayCell:(id)cell
    forTableColumn:(NSTableColumn *)col
    row:(int)row
{
	if ([col isTheColumnYouWantToSetTheImageOn]) {  // provide your own test here.
		[cell setImage: [self theAppropriateImageForThisRow: row]];
	}
}


Note, this cell will look bad when you edit it.  You'll have to subclass the cell in order to make the editor box move over to where the text is (to the right of the image.)  Presumably, Apple considers the current behavior a bug, since Finder does it correctly (but then again, it's written in Carbon and doesn't even use NSBrowserCell... oy!)

TomWaters

----

If you have an NSOutlineView, can you have the disclosure triangle on the left, then an icon, THEN the item name, like in Finder's list mode? 

-- StevenFrank

----

Steven, it is possible. I found it best to subclass NSCell and just make a method to set a string and icon value (in an NSArray) and a method to draw both. Here's an example:

    
@interface IconCell : NSCell 
{
    NSArray * cellValue;
}
- (void)setObjectValue:(id <NSCopying>)object;
- (void)drawInteriorWithFrame:(NSRect)cellFrame inView:(NSView *)controlView;

@implementation IconCell

- (void)drawInteriorWithFrame:(NSRect)cellFrame inView:(NSView *)controlView
{
    NSDictionary * textAttributes =
        [NSDictionary dictionaryWithObjectsAndKeys:[NSFont 
userFontOfSize:10.0],NSFontAttributeName, nil];
    NSPoint cellPoint = cellFrame.origin;
    
    [controlView lockFocus];
    
    cellValue objectAtIndex:1] compositeToPoint:[[NSMakePoint(cellPoint.x+2,
 cellPoint.y+14) operation:NSCompositeSourceOver];
    cellValue objectAtIndex:0] drawAtPoint:[[NSMakePoint(cellPoint.x+18,
 cellPoint.y) withAttributes:textAttributes];
    
    [controlView unlockFocus];
}

- (void)setObjectValue:(id <NSCopying>)object
{
    cellValue = (NSArray *)object;
}
@end


-Eric Schneider

----

Eric:

The only problem with that (for new developers to Cocoa) is that when subclassing NSCell, you lose a lot of the "magic" highlighting on clicks, etc. 

For a newbie, that can mean a world of trouble to re-implement...
(unless I'm wrong and there's a way of subclassing NSCell, calling [super drawInteriorWithFrame:], and drawing what you need to over it. hm... actually, I guess one could do all one's drawing, alter the frame size / origin, and then call [super blahBlahWithFrame:]...

Interesting... 

-esammer

----

Apple provides a good example of using an NSOutlineView with Drag-n-Drop.

http://developer.apple.com/mac/library/samplecode/DragNDropOutlineView/

-simon liu

----

An even better example is the **I<nowiki/>mageBackground** sample code which has an **I<nowiki/>mageAndTextCell** class that handles all the drawing and subclasses the appropriate methods.

http://developer.apple.com/mac/library/samplecode/ImageBackground/

 - Quinn Taylor

----

Tom's example works really well for me (on Panther). The only comment I'd add is that if you don't explicitly set an image for an item, the image set on the previous item will be used. You'll spot that fairly quickly, of course :-)

--Chris

----

Does Eric's technique work with CoreData bindings (A to-many relationship for an item in a single row, I mean)?

----

I read this a while back and tried to use NSBrowserCell in an NSOutlineView (acting as a source list). An important caveat is NSBrowserCell subclasses NSCell, not NSActionCell. And it's NSActionCell that exposes "value" in Cocoa Bindings as setObjectValue. This is in 10.5 though, not sure what 10.4 or 10.6 have. But you can always check an object's bindings through [self exposedBindings]. Something to keep in mind.

--BrianDunagan

