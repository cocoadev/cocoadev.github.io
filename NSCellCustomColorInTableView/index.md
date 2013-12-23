---
layout: page
title: NSCellCustomColorInTableView
---



I'm setting the color of each cell in a NSTableView by overriding the frameOfCellAtColumn method, which I also have to override so that the width of the columns in each row is different depending on what the value is in the first cell of the row.

In order to change the color, I'm filling it using the NSRectFill function. This works, but whenever I edit a value inside the table it fills in rectangles that are completely outside of the table view. I've logged the origin points of every rectangle passed to NSRectFill, and everything seems to be OK in the logs, but the artifacts are there... I'm attaching a screen shot and the code below, any help is appreciated.

http://www.charlie-roberts.com/forumImages/fillRectProblem.gif

    
- (NSRect)frameOfCellAtColumn:(int)column row:(int)row {
	int i, x = 0;

	modWin = (ModWindow *)[NSApp keyWindow];
	NSMutableArray *inputs = [modWin getInputs];
	ModInput *thisRow = [inputs objectAtIndex: row];
	
	NSRect next = [super frameOfCellAtColumn:column row:row]; // can also merge rows this way

	NSColor *cellColor = cellColors objectForKey:[[thisRow properties] objectForKey:@"v0" objectAtIndex:column];
	[cellColor set];

	//the if statement below repositions the cells according to the widths of the columns; does not affect color

	if([thisRow properties] objectForKey:@"v0"] isEqual:@"MIDI"]) {
		next.size.width = [[[[MIDIWidths objectAtIndex: column] intValue];
		for(i=0; i<column; i++) {
			x += [[MIDIWidths objectAtIndex:i] intValue];
		}
		next.origin.x = x;
	}else if([thisRow properties] objectForKey:@"v0"] isEqual:@"Keystroke"]){
		next.size.width = [[[[KeystrokeWidths objectAtIndex: column] intValue];
		for(i=0; i<column; i++) {
			x += [[KeystrokeWidths objectAtIndex:i] intValue];
		}
		next.origin.x = x;
	}else if([thisRow properties] objectForKey:@"v0"] isEqual:@"USB"]){
		if( [[[[USBWidths objectAtIndex: column] intValue] == 0){
			return NSZeroRect;
		}
		next.size.width = [[USBWidths objectAtIndex: column] intValue];
		for(i=0; i<column; i++) {
			x += [[USBWidths objectAtIndex:i] intValue];
		}
		next.origin.x = x;
	}
	
	if(!([super editedColumn] == column && [super editedRow] == row)) {
		NSRectFill(next);
	}

	return next;
}


----
Well, I managed to partially fake out the problem by adding a _reset flag that determines whether or not NSRectFill() is called. I set this flag to be false in the     - (BOOL)tableView:(NSTableView *)aTableView shouldEditTableColumn:(NSTableColumn *)aTableColumn row:(int)rowIndex delegate method, and set it to be true when they actually begin editing the text. For some reason the errant rectangles are drawn in between these two events... I'm thinking maybe it has something to do with the fieldEditor but I have not idea what the problem really is.

However, I still get the freakazoid rectangles when I do drag and drop....

----
It might have something to do with the field editor.  You can try saving the graphics state and appending a clipping region before drawing.  Just remember to restore the graphics state.

----
Thanks so much for the suggestion. The problem persists even with a clipping rectangle in place... it's gotta be a glitch. You can see where I've defined the clipping rectangle in the top tableview, but then those stupid other rectangles are still popping up... outside of the clipping rect, outside of the table view and outside of the NSView object containing the tableview! So frustrating...

http://www.charlie-roberts.com/forumImages/lameRectangles.gif

I just used this code to draw the rectangle:

    
if(!([super editedColumn] == column && [super editedRow] == row)) { // this only fills the cell if it's not currently being edited
	NSGraphicsContext *gc = [NSGraphicsContext currentContext];
	[gc saveGraphicsState];
	[NSBezierPath clipRect:NSRectFromString(@"20 50 300 300")]; 
	[NSBezierPath fillRect:next];
	[gc restoreGraphicsState];
}


Thanks again.

----

Does the problem occur if you use NSRectClip and NSRectFill instead of the NSBezierPath methods? Also, you should probably use NSMakeRect instead of NSRectFromString for performance reasons; the former is inlined and has almost no overhead, while the latter is declared extern and involves object manipulation.

----
The problem is still there if I use NSRectClip and NSRectFill... it refuses to die. One other weird thing to note: As I click in rectangles further down in the tableView, the artifact rectangles travel -up- from the bottom of the window. So, clicking close to the top of the table view yields an artifact at the bottom of the window. Also, the artifact rectangles disappear as soon as the view of the window is refreshed. Actually, I guess that part makes sense...

Thanks for the tip about NSMakeRect!

----
You're drawing directly in     -frameOfCellAtColumn:row: but this method can be called for lots of reasons that don't involve drawing, in which case you'll be drawing into random graphics contexts and probably causing problems like this.

----

The post above this one is exactly right. You're performing drawing in a method that's not exclusive to drawing and thereby completely ignoring / trampling all over Cocoa's drawing machinery. I strongly suggest you read the Cocoa drawing guide; you're completely missing vital knowledge. Why on earth aren't you using the Cell functionality Cocoa gives you for free? Create your own NSCell subclass and do your own drawing. With a text cell, you might even be able to get away with using NSTableView's     -tableView:willDisplayCell:forTableColumn:row: delegate method.

