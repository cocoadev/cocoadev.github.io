---
layout: page
title: PreventWindowOrdering
---

Adding these methods to your NSView subclass will prevent the window from becoming active when clicking on it.

    
- (BOOL)shouldDelayWindowOrderingForEvent:(NSEvent *)theEvent
{
        return YES;
}

- (BOOL)acceptsFirstMouse:(NSEvent *)theEvent
{
        return YES; 
}

- (void)mouseDown:(NSEvent *)theEvent
{
        [NSApp preventWindowOrdering]; 
}


-- (Originally from) RyanBates

----

**The Basic Question**

How can I accept mouse events, dragging etc without becoming the front-most window? (ie. stay in the background but still be interactive)?

----

- (BOOL)shouldDelayWindowOrderingForEvent:(NSEvent *)theEvent

Overridden by subclasses to allow the user to drag images from the receiver without its window moving forward and possibly obscuring the destination and without activating the application. If this method returns YES, the normal window-ordering and activation mechanism is delayed (not necessarily prevented) until the next mouse-up event. If it returns NO, then normal ordering and activation occur. Never invoke this method directly; it�s invoked automatically for each mouse-down event directed at the NSView.
An NSView subclass that allows dragging should implement this method to return YES if theEvent, an initial mouse-down event, is potentially the beginning of a dragging session or of some other context where window ordering isn�t appropriate. This method is invoked before a mouseDown: message for theEvent is sent. NSView�s implementation returns NO.

If, after delaying window ordering, the receiver actually initiates a dragging session or similar operation, it should also send a preventWindowOrdering message to NSApp, which completely prevents the window from ordering forward and the activation from becoming active. preventWindowOrdering is sent automatically by NSView�s dragImage:... and dragFile:... methods.

----

**Application**

I'm trying to make an app that is similar to the floating clock app in OS X (in Panther, go to Date and Time and choose "View In Window").

What I've done to try to copy this is create a window and then putting a view inside that window.  I make the window invisible, and then make the view however I want it to be.  I set the window level to NSStatusWindowLevel so that 

One thing I'm having trouble with is getting the dragging action right.  I would like to be able to just click, hold and drag, even if the app isn't active.  As it stands I have to click once on the window to make it active, and then I'm able to drag it around.  How can avoid having to do this extra click?

Second, I notice that when I click on the clock window, the app doesn't change.  But when I click my app, the menu bar changes to my app's menu bar.

*The solution is to implement the methods indicated in this discussion*

See also DragMatrix or other ways to use this approach

