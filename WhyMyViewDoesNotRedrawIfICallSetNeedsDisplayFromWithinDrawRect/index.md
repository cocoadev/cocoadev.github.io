---
layout: page
title: WhyMyViewDoesNotRedrawIfICallSetNeedsDisplayFromWithinDrawRect
---

**Question:
I am calling [self setNeedsDisplay:YES] from within my view's implementation of -drawRect:, but my view doesn't redraw.  Why ?**

**Answer:**
The General/AppKit effectively sets a flag whenever -setNeedsDisplay: is called for a view.  The fact that the flag is set causes General/AppKit to send 
a -drawRect: message to the view and appropriate sub-views at a later time.  After the -drawRect: is called, the flag is cleared.  [This over-simplifies it, but illustrates the concept.]

If you call -setNeedsDisplay: from within -drawRect:, the flag will be set, but as soon as -drawRect: returns, it will be cleared again.  
Therefore, when the time arrives, the flag is clear and -drawRect: is not called again.

Furthermore, do not call -display or variants or -drawRect: from within -drawRect: or you invite an infinite recursion and crash.

----
**Question: I was calling [self setNeedsDisplay:YES] because I am repositioning some subviews within my view's implementation of -drawRect:.  How can I cause a redraw so that the repositioned subviews draw correctly ?**

**Answer:**
-drawRect should only be used for drawing.  Resizing, repositioning, and adding or removing subviews absolutely should not be done in 
-drawRect:.  Incorrect behavior will likely result.  The General/AppKit reasonably relies on the view hierarchy remaining constant while it is in 
the middle of drawing the view hierarchy.

If you want to rearrange subviews, I recommend that you do it in a method like - (void)setFrame:(General/NSRect)frameRect or - (void)resizeSubviewsWithOldSize:(General/NSSize)oldBoundsSize.  You might even like General/NSViewFrameDidChangeNotification.

There is a whole section in Apple's General/NSView documentation on resizing behavior for views and subviews.

Some other General/NSView subclasses use methods like -tile found in General/NSScrollView.

*
- (void)tile

Lays out the components of the receiver: the content view, the scrollers, and the ruler views. You rarely need to invoke this method, but subclasses may override it to manage additional components
*

Perhaps you want to provide a similar method and call it whenever your view's frame changes or some other stimulus happens.
