---
layout: page
title: NSCellRollover
---

I'm attempting to create an NSCell to be used in a tableView similar to the "Eject" icon in iTunes, Finder, etc. I've got an NSImageCell subclass, and I'm dealing with changing the image depending on if the row is selected in my controller. However, I can not for the life of me figure out how to implement the rollover effect for my cell. I've searched the archives, and I can tell that it is going to require some mouse tracking, but I can't figure out how exactly to implement it. Any help would be appreciated.

-- MattBall

*Take a look at NSTableViewRollover.  It's probably pretty close to what you are looking for.*

----

That doesn't seem to be exactly what I'm looking for. I'd REALLY like to be able to track the mouseOver event from within the cell itself. If that's not possible, then I need to know how to refer to a cell at the intersection of two rects. I have this in my tableView:

    
- (void)awakeFromNib
{
	//[self setRowHeight: 40];
	self window] setAcceptsMouseMovedEvents:YES];
	trackingTag = [self addTrackingRect:[self frame] owner:self userData:nil assumeInside:NO];
	mouseOverView = NO;
	mouseOverRow = -1;
	lastOverRow = -1;
}
- (void)mouseEntered:([[NSEvent *)theEvent
{
	mouseOverView = YES;
}

- (void)mouseMoved:(NSEvent *)theEvent
{
	if(mouseOverView) {
		mouseOverRow = [self rowAtPoint:[self convertPoint:[theEvent locationInWindow] fromView:nil]];
		mouseOverColumn = [self columnAtPoint:[self convertPoint:[theEvent locationInWindow] fromView:nil]];
		if(mouseOverColumn == 2) {
			// This is where I need to be able to refer to the column at the intersection of mouseOverRow and mouseOverColumn
		}
	}
}


I know that NSTableView provides a way to get the intersection rectangle, but I still don't know how to use that to reference an individual cell.

-- MattBall

----

Nevermind. I got it working using NSTableView's -setNeedsDisplayInRect: to call -tableView:willDisplayCell:forTableColumn:row:, which let me reference the cell.

-- MattBall

