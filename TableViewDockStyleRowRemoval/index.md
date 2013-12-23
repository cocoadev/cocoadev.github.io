---
layout: page
title: TableViewDockStyleRowRemoval
---

I have a General/NSTableView that I want to behave like the Dock when items are dragged out of it - the item disappears in a puff of smoke and is removed from the view. Any pointers on how to do that?
----
Have a look at the General/NSShowAnimationEffect function.

----

OK, I've done some work and got this *looking* right, but it's not actually doing anything. I drag the row out of the table view and I get the poof effect, but the drag snaps back and the row doesn't get removed. Here's my code:

    
@implementation General/MyTableViewSubclass

- (unsigned int)draggingSourceOperationMaskForLocal:(BOOL)isLocal
{
    return General/NSDragOperationDelete;
}
- (void)draggedImage:(General/NSImage *)anImage endedAt:(General/NSPoint)aPoint operation:(General/NSDragOperation)operation
{
    if ( !General/NSMouseInRect(General/self window] convertScreenToBase:aPoint], [self bounds], NO) ) //is outside views bounds
    {
	[[NSShowAnimationEffect(General/NSAnimationEffectDisappearingItemDefault,
			      aPoint, General/NSZeroSize, nil, nil, nil);
	
	General/self dataSource] tableView:self deleteRows:[self selectedRowIndexes;
    }
}
@end

---

@implementation General/MyTableViewDatasource

- (BOOL)tableView:(General/NSTableView *)tableView writeRows:(General/NSArray *)rows toPasteboard:(General/NSPasteboard *)pboard
{
    return YES;
}

- (BOOL)tableView:(General/NSTableView *)sender deleteRows:(General/NSIndexSet *)rows
{
    unsigned i = [rows firstIndex];
    
    while (i < [rows lastIndex]) 
    {
        int curRow = i;
        [tvArray removeObjectAtIndex:curRow];
	i = [rows indexGreaterThanIndex: curRow];
    }
    
    [sender reloadData];
    return YES;
}

@end


This has 2 (unrelated, I think) problems

1. It will only work if the row is selected, not simply clicked and dragged out of the view

2. It's not working anyway! Nothing is removed from the table after     reloadData. Do I need to actually write the rows to the pasteboard in     - (BOOL)tableView: writeRows: toPasteboard: even if I'm just going to immediately delete them?


The following General/NSView methods look like what I need, but I'm not sure how to implement them?

    - (BOOL)dragFile:(General/NSString *)fullPath fromRect:(General/NSRect)aRect slideBack:(BOOL)slideBack event:(General/NSEvent *)theEvent

    - (void)dragImage:(General/NSImage *)anImage at:(General/NSPoint)imageLoc offset:(General/NSSize)mouseOffset event:(General/NSEvent *)theEvent pasteboard:(General/NSPasteboard *)pboard source:(id)sourceObject slideBack:(BOOL)slideBack

Any help is appreciated.

-John

----

If the row is not selected, you get the row that is being dragged via writeRows:(General/NSArray *)rows. This alleviates problem number one. I prefer to always use that array, rather than using the selected row, as you probably noticed with your issues.

As for number two, remove the item you are dragging immediately as the drag starts. If the row is dropped back on the table, put it back in place. That is the way the dock works, and I am assuming that is how you want it. -- General/MatPeterson

----


I'm having a heck of a time figuring this out. Can anyone post an example of this?

----

Simply add this to your General/NSTableView subclass:

- (void)dragImage:(General/NSImage *)anImage at:(General/NSPoint)imageLoc offset:(General/NSSize)mouseOffset
	event:(General/NSEvent *)theEvent pasteboard:(General/NSPasteboard *)pboard source:(id)sourceObject slideBack:(BOOL)slideBack {
	// Prevent slide back.
	[super dragImage:anImage at:imageLoc offset:mouseOffset event:theEvent pasteboard:pboard source:sourceObject slideBack:NO];
}

General/JulianCain
