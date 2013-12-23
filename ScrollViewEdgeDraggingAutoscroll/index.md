---
layout: page
title: ScrollViewEdgeDraggingAutoscroll
---



This topic discusses how a scroll view automatically scrolls its document view when a drag and drop action is in progress and the item (being dragged) is over an edge of a scroll view. The default behavior is for the scroll view to nudge its contents over in the direction of the edge (e.g. if the dragged item is hovering over the left edge of a scroll view the document view will scroll to the left automatically). Below, is one way you can override this behavior. 

You can selectively autoscroll in user defined directions by subclassing the enclosing scroll view and overriding     scrollClipView:toPoint:. For example, if a table view is enclosed in a scroll view, and you would like the scroll view to "only" automatically scroll up and down when an item is being dragged over it. You can control the autoscrolling behavior by overriding     scrollClipView:toPoint:. 

    
- (void)scrollClipView:(NSClipView *)aClipView toPoint:(NSPoint)newOrigin {
	if (gIsPerformingDragOperation) 
		newOrigin.x = [aClipView bounds].origin.x;
	[super scrollClipView:aClipView toPoint:newOrigin];
}


To bypass autoscrolling left when items are dragged over the visible left edge of a document view, simply modify the new origin to keep the original x value. Notice, this modification will still allow the clip view to be scrolled vertically.

    gIsPerformingDragOperation is a global BOOL variable that is turned on during a drag operation. I simply asked the document view of the custom scroll view to register for most of the common dragged types and then implemented the appropriate drag and drop methods to switch gIsPerformingDragOperation on and off. The better solution would be to find a function or method that can tell me if a drag operation is in progress (I'm still searching for one). 

    
- (NSDragOperation)draggingEntered:(id <NSDraggingInfo>)sender {
    gIsPerformingDragOperation = YES;
}
- (NSDragOperation)draggingUpdated:(id <NSDraggingInfo>)sender {
    gIsPerformingDragOperation = YES;
}
- (NSDragOperation)draggingExited:(id <NSDraggingInfo>)sender {
    gIsPerformingDragOperation = NO;
}
- (NSDragOperation)concludeDragOperation:(id <NSDraggingInfo>)sender {
    gIsPerformingDragOperation = NO;
}


The motive for wanting this type of behavior is simple. I would like to keep table columns that were visible before a drag operation started to stay visible while a drag operation is in progress. --zootbobbalu

