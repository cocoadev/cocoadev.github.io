---
layout: page
title: RightClickSelectInTableView
---



There seems to be no solid explanation of how to cause a right- or control-clicked NSTableView to select a row. This is especially useful for using context menus to operate on a particular cell's content. Any demonstrations or examples would be greatly appreciated.

----

I have made a subclass of NSTableView that overrides menuForEvent. This method calls the dataSource for the right-click menu and selects the row first. �TimothyHatcher

Like so:

    
- (NSMenu *) menuForEvent:(NSEvent *) event {
	NSPoint where;
	int row = -1, col = -1;

	where = [self convertPoint:[event locationInWindow] fromView:nil];
	row = [self rowAtPoint:where];
	col = [self columnAtPoint:where];

	if( row >= 0 ) {
		NSTableColumn *column = nil;
		if( col >= 0 ) column = self tableColumns] objectAtIndex:col];

		if( [[self delegate] respondsToSelector:@selector( tableView:shouldSelectRow: )] ) {
			if( [[self delegate] tableView:self shouldSelectRow:row] )
				[self selectRow:row byExtendingSelection:NO];
		} else [self selectRow:row byExtendingSelection:NO];
	
		if( [[self dataSource] respondsToSelector:@selector( tableView:menuForTableColumn:row: )] )
			return [[self dataSource] tableView:self menuForTableColumn:column row:row];
		else return [self menu];
	}

	[self deselectAll:nil];
	return [self menu];
}


----
This doesn't handle shift and command key selection behavior. Unfortunately, there isn't a "correct" way to handle shift selection extension since _lastSelectedRow isn't a public member of [[NSTableView. Submitted as Radar #3885165.

-- AlexRosenberg

----

I recently had reason to change a row selection before showing the right-click menu.  This code worked and seems less involved than what is above.  Note that it uses NSIndexSet and is therefore 10.3-only but can easily be changed to be backwards compatible using the old method.

    

- (void)rightMouseDown:(NSEvent *)theEvent
{	
	NSPoint p = [self convertPoint:[theEvent locationInWindow] fromView:nil];
	
	int i = [self rowAtPoint:p];
	
	if (i < [self numberOfRows])
		[self selectRowIndexes:[NSIndexSet indexSetWithIndex:i] byExtendingSelection:NO];

	[NSMenu popUpContextMenu:[self menu] withEvent:theEvent forView:self];
}



*What happens when someone is using software to swap the right and left mouse buttons? This is more common than you think and will bite you in the ass at some point. ;-)*

If the user changes something that is out of our control, it is not our problem rather theirs, we can notify them, but other then that we are making things to the standard way.

*menuForEvent will do basically the same thing and (if modified from above) will handle all of this while honoring mouse button remapping. Like key mapping, mouse button mapping should be respected as well. Why say "it's there problem" when in reality YOU'RE the one they'll run to when your app doesn't work like all the other well-behaved apps because they're using <insert non-standard wiz-bang system swizzler here> and don't tell you about it/assume it's your app's fault. Personally, I think the above would be a very short-sighted assumption that'll only cause problems and so, I would avoid it.*

I know users could swap the right and left mouse buttons, but I'm not terribly concerned.  Using MouseWorks with a Kensington mouse, if you switch them up, it still receives the right mouse click event.  I would hope that other remapping methods do about the same.  Does anyone have any information about how other mouse drivers handle it?  My assumption in writing the above code wasn't that users wouldn't switch buttons but rather that switching buttons didn't make a difference to how the app handled it.

----

I added a minor addition to the above code so that it preserves the existing row selection if the row you right-clicked on was in the existing selection.  Thus, if you select, say, 5 rows in your table and then right-click on any of those 5 rows, you will want to preserve that selection so the action you are selecting in the contextual menu will apply to all 5 rows.

    

- (void)rightMouseDown:(NSEvent *)theEvent {       
	NSPoint p = [self convertPoint:[theEvent locationInWindow] fromView:nil];
	
	int i = [self rowAtPoint:p];
	
	if (i < [self numberOfRows] && !self selectedRowIndexes] containsIndex:i]) {
		[self selectRowIndexes:[[[NSIndexSet indexSetWithIndex:i] byExtendingSelection:NO];
	}
	
	[NSMenu popUpContextMenu:[self menu] withEvent:theEvent forView:self];
}



----

N.B. rightMouseDown: won't catch control-clicks, which is still what most Mac users are using.

----

I wrote some similar code for an NSBrowser here: RightClickSelectInBrowser

----

Here's an alternative way of doing the same thing --KevinWojniak
    - (NSMenu *)menuForEvent:(NSEvent *)theEvent
{
  if ([theEvent type] == NSRightMouseDown)
  {
    NSPoint mousePoint = [self convertPoint:[theEvent locationInWindow] fromView:nil];
    int row = [self rowAtPoint:mousePoint];
    if (row >= 0)
    {
      [self selectRow:row byExtendingSelection:NO];
    }
    else
    {
      // you can disable this if you don't want clicking on an empty space to deselect all rows
      [self deselectAll:self];
    }
  }

  // make sure you return something!
  return [super menuForEvent:theEvent];
}

----

Same as above but removed the "if NSRightMouseDown" check which is redundant and breaks support for control-clicking:

    - (NSMenu *)menuForEvent:(NSEvent *)theEvent
{
    NSPoint mousePoint = [self convertPoint:[theEvent locationInWindow] fromView:nil];
    int row = [self rowAtPoint:mousePoint];
    if (row >= 0)
    {
      [self selectRow:row byExtendingSelection:NO];
    }
    else
    {
      // you can disable this if you don't want clicking on an empty space to deselect all rows
      [self deselectAll:self];
    }

  // make sure you return something!
  return [super menuForEvent:theEvent];
}

Similar to above, but don't deselect other rows if the row the user clicked on is currently selected. (Allowing the contextual menu to operate on all selected items)
    
- (NSMenu *)menuForEvent:(NSEvent *)theEvent
{
	if ([theEvent type] == NSRightMouseDown)
	{
		// get the current selections for the outline view. 
		NSIndexSet *selectedRowIndexes = [self selectedRowIndexes];
		
		// select the row that was clicked before showing the menu for the event
		NSPoint mousePoint = [self convertPoint:[theEvent locationInWindow] fromView:nil];
		int row = [self rowAtPoint:mousePoint];
		
		// figure out if the row that was just clicked on is currently selected
		if ([selectedRowIndexes containsIndex:row] == NO)
		{
			[self selectRow:row byExtendingSelection:NO];
		}
		// else that row is currently selected, so don't change anything.
	}
	
	return [super menuForEvent:theEvent];
}

