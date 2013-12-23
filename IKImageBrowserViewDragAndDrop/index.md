---
layout: page
title: IKImageBrowserViewDragAndDrop
---

I've added an IKImageBrowserView to my app in a Utility Panel.  I want to be able to drag and drop files onto it, so I set the delegate using the 
    
- (void) setDraggingDestinationDelegate:(id) delegate;


and registered the view with registerForDraggedTypes:

When I drag a file into the View the delegate receives draggingEntered: from which I return NSDragOperationCopy.  However, the Copy Cursor(cursor with the green plus bubble) only appears when the mouse is on the single pixel wide border around the outside of the View.  If I move the drag further, the copy symbol goes away, but I do not receive dragExited: or dragEnded:.  If I try to drop the files on the View, I then get dragExited: and dragEnded:, but I never receive prepareForDragOperation: or performDragOperation:.

Does anyone know what I'm missing to get this working?

----
Do you also implement     draggingUpdated:? If not, then you could be catching the superclass's implementation which would intefere with yours by, say, refusing drag operations.

----
No, I had not implemented that one.  That fixed my problem.  I implemented it and return NSDragOperationCopy.

I had been following Apple's documentation:

*
As the dragging session continues, the destination is sent draggingUpdated: messages. You only need to implement this if the destination needs to know the current position of the dragged image, either to change the dragging operation or to update any visual feedback, such as an insertion point, you are providing. If not implemented, NSView assumes the dragging operation is unchanged from the draggingEntered: invocation.
*

I didn't take into account that the superclass may have implemented it, which I had to then override.  Thanks!

