---
layout: page
title: CustomNSScrollView
---

What's the best way to subclass NSScrollView or otherwise access it so that I can create my own scroller. All I really need is to create two buttons that will act as the up and down scroll buttons, I don't need the bar in between. Can an action be sent that will tell the NSScrollView to go down or up one line or page? --LoganCollins

----

I can't tell what you really want.  Mac OS X and Cocoa already supprt line and page scroll for every scroll view.  You just need to provide values via - (void)setVerticalLineScroll:(float)aFloat and - (void)setVerticalPageScroll:(float)aFloat.  See documentation copied below.

If ypu really want to control how a scroll view draws itself, see the -tile method.  You can use that method to add sub-controls, hide sub-controls, remove sub-controls and otherwise mess with what scroll views do.  You can also deconstruct the scroll view and find out what actions or other methods the up and down arrows within a scroll bar call.

Finally, the fact that you can post to this forum suggests that you could look all of this up yourself.  If I don't understand what you really want or the cited documentation doesn't help you, I suggest you do a google searchs for examples of NSScrollView subclasses.


Apple's documentation:

setVerticalLineScroll:
- (void)setVerticalLineScroll:(float)aFloat

Sets the amount by which the receiver scrolls itself vertically when scrolling line by line to aFloat, expressed in the content view�s coordinate system. This value is the amount used when the user clicks the scroll arrows on the vertical scroll bar without holding down a modifier key. When displaying text in an NSScrollView, for example, you might set this value to the height of a single line of text in the default font.
See Also: � verticalLineScroll, � setHorizontalLineScroll:, � lineScroll, � setVerticalPageScroll:

setVerticalPageScroll:
- (void)setVerticalPageScroll:(float)aFloat

Sets the amount of the document view kept visible when scrolling vertically page by page to aFloat, expressed in the content view�s coordinate system. This amount is used when the user clicks the scroll arrows on the vertical scroll bar while holding down the Option key.
This amount expresses the context that remains when the receiver scrolls by one page, allowing the user to orient to the new display. It differs from the line scroll amount, which indicates how far the document view moves. The page scroll amount is the amount common to the content view before and after the document view is scrolled by one page. Thus, setting the page scroll amount to 0.0 implies that the entire visible portion of the document view is replaced when a page scroll occurs.

See Also: � verticalPageScroll, � setHorizontalPageScroll:, � pageScroll, � setVerticalLineScroll:

verticalLineScroll
- (float)verticalLineScroll

Returns the amount by which the receiver scrolls itself vertically when scrolling line by line, expressed in the content view�s coordinate system. This amount is used when the user clicks the scroll arrows on the vertical scroll bar without holding down a modifier key.
See Also: � setVerticalLineScroll:, � horizontalLineScroll, � setLineScroll:, � verticalPageScroll

verticalPageScroll
- (float)verticalPageScroll

Returns the amount of the document view kept visible when scrolling vertically page by page, expressed in the content view�s coordinate system. This amount is used when the user clicks the scroll arrows on the vertical scroll bar while holding down the Option key.
This amount expresses the context that remains when the receiver scrolls by one page, allowing the user to orient to the new display. It differs from the line scroll amount, which indicates how far the document view moves. The page scroll amount is the amount common to the content view before and after the document view is scrolled by one page.

See Also: � setVerticalPageScroll:, � horizontalPageScroll, � setPageScroll:, � verticalLineScroll

