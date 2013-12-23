---
layout: page
title: TrackingRectangles
---

Tracking rectangles are a feature of     General/NSView and     General/NSWindow which allows you to specify a set of rectangles within an     General/NSView that will trigger method calls when the mouse pointer either enters or exits the rectangle.

General/CursorRects are a closely related concept. They are a kind of tracking rectangle that specifies which cursor will be displayed when the mouse is within them.

Some advice for using tracking rectangles:


*Don't try and set up a tracking rectangle in your view's     -initWithFrame: method. Tracking rectangles actually
get added to the window the view is on, they aren't kept track of by the view itself. When you set up the rects in     -initWithFrame: 
your view hasn't been added to its window yet so the tracking rectangles aren't being set up. Set them up in     -viewDidMoveToWindow instead.
*Alternately, you can set up tracking rectangles in     -resetCursorRects. This will work fine because     -resetCursorRects is called in circumstances where both cursor and tracking rectangles need to be reestablished. However, note that you need to keep track of your tracking rectangle tags and remove them in     -resetCursorRects before re-adding them.
*To receive tracking rectangle events, you are **not** required to call     -setAcceptsMouseMovedEvents
*Tracking rectangles (and General/CursorRects) do not normally work inside windows created with the     General/NSBorderlessWindowMask style because a borderless window by default can not become the key window. Override     -canBecomeKeyWindow to return     YES to enable these features. See General/BorderlessWindow.


----

General/AppKit documentation -> *window may return nil when this method is invoked, indicating that the receiver does not currently reside in any window. This occurs when the receiver has just been removed from its superview or when the receiver has just been added to a superview that does not itself have a window. Overrides of this method may choose to ignore such cases if they are not of interest.*

    
- (void)viewDidMoveToWindow {
    if ([self window]) {
        /*
        add your tracking rect code here
        */
    }
}


--zootbobbalu
