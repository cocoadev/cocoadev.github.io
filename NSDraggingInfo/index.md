---
layout: page
title: NSDraggingInfo
---

http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/Protocols/NSDraggingInfo_Protocol/

Constants that are returned by <code>draggingSourceOperationMask</code>

*NSDragOperationCopy - The data represented by the image can be copied.
*NSDragOperationLink - The data can be shared.
*NSDragOperationGeneric - The operation can be defined by the destination.
*NSDragOperationPrivate - The operation is negotiated privately between the source and the destination.
*NSDragOperationMove - The data can be moved.
*NSDragOperationDelete - The data can be deleted.
*NSDragOperationEvery - All of the above.
*NSDragOperationNone - No drag operations are allowed.


(The following lines are from <code>NSDragging.h</code>)

    
/* protocol for the sender argument of the messages sent to a   
drag destination.  The view or window that registered dragging types sends these messages as dragging is
 happening to find out details about that session of dragging.
 */
@protocol NSDraggingInfo
- (NSWindow *)draggingDestinationWindow;
- (NSDragOperation)draggingSourceOperationMask;
- (NSPoint)draggingLocation;
- (NSPoint)draggedImageLocation;
- (NSImage *)draggedImage;
- (NSPasteboard *)draggingPasteboard;
- (id)draggingSource;
- (int)draggingSequenceNumber;
- (void)slideDraggedImageTo:(NSPoint)screenPoint;
@end

