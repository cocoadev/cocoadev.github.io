---
layout: page
title: NSTextViewOverlay
---

I'm trying to draw small indicator images over-top the text in an NSTextView. I want my custom NSView to overlay the text view's enclosing scroll view (drawing the images only for the visible region). I'd *really* like it to be all self-contained, so I added it to my NSTextView subclass as one .m/.h pair (don't know the proper term).

On awakeFromNib, the NSTextView creates an OverlayView and gives it its rules (what to look for and draw over). On drawRect, the OverlayView then finds all the coordinates of the place to draw, then composites the special images onto itself. This all works just fine.

HOWEVER:

I'm having trouble figuring out to which view to attach the OverlayView, what to listen for to redraw only when needed (during scroll, during typing, etc.). Assuming my NSTextView is enclosed in a standard NSScrollView, to which view do I attach my OverlayView so that it's always in the right place and always updating appropriately?

----

Your custom view should be a subview of the NSTextView subclass. Then in your NSTextView subclass you can override -(void)textDidChange and update your view. Since the custom view is a subview of the TextView, scrolling and everything will already be handled.

----

For suggestions about this and related issues, see ControlsMixedWithTextHowTo and topics related to NSTextAttachment

