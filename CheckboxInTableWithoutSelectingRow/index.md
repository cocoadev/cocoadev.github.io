---
layout: page
title: CheckboxInTableWithoutSelectingRow
---


----

Anyone know how to set NSTableView (or its delegate) up so that I can flip checkboxes in one of its columns without the row that the checkbox sits in becoming selected? The delegate implements     tableView:shouldSelectRow: but it doesn't tell me which column I'm clicking - for clicks in other columns selection should work normally. --GrahamCox

----
Stupid question first: does     tableView:shouldSelectRow: or     tableView:setObjectValue:forTableColumn:row: get called first? If it's     setObject..., you can just remember to ignore the next selection. --JediKnil

----

    shouldSelectRow; gets called before     setObjectValue: unfortunately. Also, in the case where the row is already selected,     shouldSelectRow: isn't called at all, though for my purposes that doesn't matter. However, if you were to set a flag on one method and clear it on the other, you wouldn't always get the call to clear it, leaving it the wrong state. Sigh. All for the lack of passing the column to     shouldSelectRow:. --GC

----

It's looking like it can't be done. If I refuse the selection, the checkbox doesn't get flipped anyway, so selectively refusing the selection according to column doesn't help. That's pretty annoying, as using a table in this way is not that unusual. There are plenty of times when you might want to flip a flag in an object that is in the table but not selected - think about turning off the visibility of layers in a stack for example. Looks like I might have to subclass the table, or maybe do something skanky in the cell. --GC

----

OK, I have a solution that works for my requirements, though may not be completely general. It requires subclassing both the table and the button cell, which is a lot of work for the stated need here in my opinion, but as far as have been able to tell, it's the only way. Basically you need to override the mouseDown: for the table as a whole so that you can get the click before it selects anything - it seems as if selecting the row is the very first thing the table view does. The next problem is that having intercepted the click at this point, the standard tracking call to the button cell doesn't work quite how I expect it to - I don't get any visual feedback. So I ended up overriding this as well. This is probably not needed, but I couldn't find out the magic sequence needed - maybe someone with more knowledge of the internals of table views and cells can figure it. Anyhoo, here's the code:

    

@implementation GCTableView


- (void)		mouseDown:(NSEvent*) event
{
	NSPoint p = [self convertPoint:[event locationInWindow] fromView:nil];
	
	// which column and cell has been hit?
	
	int column = [self columnAtPoint:p];
	int row = [self rowAtPoint:p];
	NSTableColumn* theColumn = self tableColumns] objectAtIndex:column];
	id dataCell = [theColumn dataCellForRow:row];
	
	// if the checkbox column, handle click in checkbox without selecting the row

	if ([dataCell isKindOfClass:[[[NSButtonCell class]])
	{
		// no way to get the button type for further testing, so we'll plough on blindly
		
		NSRect	cellFrame = [self frameOfCellAtColumn:column row:row];
		
		// track the button - this keeps control until the mouse goes up. If the mouse was in on release,
		// it will have changed the button's state and returns YES.
		
		if ([dataCell trackMouse:event inRect:cellFrame ofView:self untilMouseUp:YES])
		{
			// call the delegate to handle the checkbox state change as normal
			self delegate] tableView:self setObjectValue:dataCell forTableColumn:theColumn row:row];
		}
	}
	else
		[super mouseDown:event];	// for all other columns, work as normal
}



@end


@implementation [[GCCheckboxCell


- (BOOL)	trackMouse:(NSEvent *)theEvent inRect:(NSRect)cellFrame ofView:(NSView *)controlView untilMouseUp:(BOOL)untilMouseUp
{
	NSLog(@"tracking in checkbox starting");
	
	[self setHighlighted:YES];
	[controlView setNeedsDisplayInRect:cellFrame];

	// keep control until mouse up
	
	NSEvent*	evt;
	BOOL		loop = YES;
	BOOL		wasIn, isIn;
	int			mask = NSLeftMouseUpMask | NSLeftMouseDraggedMask;
	
	wasIn = YES;
	
	while( loop )
	{
		evt = controlView window] nextEventMatchingMask:mask];
	
		switch([evt type])
		{
			case [[NSLeftMouseDragged:
			{
				NSPoint p = [controlView convertPoint:[evt locationInWindow] fromView:nil];
				isIn = NSPointInRect( p, cellFrame );
				
				if ( isIn != wasIn )
				{
					[self setHighlighted:isIn];
					[controlView setNeedsDisplayInRect:cellFrame];
					wasIn = isIn;
				}
			}
			break;
			
			case NSLeftMouseUp:
				loop = NO;
				break;
		
			default:
				break;
		}
	
	}

	[self setHighlighted:NO];
	
	// if the mouse was in the cell when it was released, flip the checkbox state
	
	if ( wasIn )
		[self setIntValue:![self intValue]];
		
	[controlView setNeedsDisplayInRect:cellFrame];

	NSLog(@"tracking in checkbox ended");
	
	return wasIn;
}


@end




I hope this will help someone avoid spending the 6 hours I just wasted on this. It really should be easier :( --GC

----

Gosh I'm having the same type of issue with trying to prevent selection changes while editing a table cell, and while I have a similar work around, it'd be really nice to have a better solution. -- Hans Hansen

----
This isn't exactly what you are trying to do, but it may help find a solution. I was trying to allow checkboxes in a table view to be checked without any row selection at all. In other words, I wanted the table view to behave as a list of check boxes, and never have a selection. 

After lots of fiddling, I found that I could just implement one delegate method, and get the desired behavior:

    
-(void)tableViewSelectionIsChanging:(NSNotification *)aNotification {
    // Prevent row selection
    aNotification object] deselectAll:self];
}


This may help with the other cases above too --- I'm not sure.

-- [[DrewMcCormack

----

Problem is that will stop the table working in other cases where you want a selection, just not when you click a checkbox. In Leopard there is:

    
- (BOOL) tableView:(NSTableView*) tableView shouldTrackCell:(NSCell*) cell forTableColumn:(NSTableColumn*) tableColumn row:(NSInteger) row


which addresses this - for Tiger you're stuck with a hack (mine or someone else's). -GC

----

Concerning the clicked column in     tableView:shouldSelectRow:, you can also find it out via     aTableView window] currentEvent] by checking if it is indeed a mouse event (also key events like cursor up/down may move the selection) and then calling     columnAtPoint: with the converted     locationInWindow. I've only tried it with an [[NSOutlineView, but it should also be working with the superclass NSTableView.

    
- (BOOL)tableView:(NSTableView *)aTableView shouldSelectRow:(NSInteger)rowIndex
{
	NSEvent *currentEvent = aTableView window] currentEvent];
	if([currentEvent type] != [[NSLeftMouseDown) return YES;
	// you may also check for the NSLeftMouseDragged event
	// (changing the selection by holding down the mouse button and moving the mouse over another row)
	int columnIndex = [aTableView columnAtPoint:[aTableView convertPoint:[currentEvent locationInWindow] fromView:nil]];
	// if the mouse moves out e.g. to the right, the column index is equal to the column count,
	// which means no real column with this index exists
	if(columnIndex < 0 || columnIndex >= aTableView tableColumns] count]) return YES;
	[[NSTableColumn *column = [[aTableView tableColumns] objectAtIndex:columnIndex];
	return ![[column identifier] isEqualToString:@"checkbox"];
}


Of course you need (at least) Leopard, as pointed out above, 'cause former system versions do not allow clicking without selecting.

--G.Pfister

