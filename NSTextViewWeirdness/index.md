---
layout: page
title: NSTextViewWeirdness
---

I have an NSTextView that I set-up so that it doesn't wrap text lines.

The problem is that when you change the bounds of the view (by growing the container window for example), the horizontal scrollbar of the enclosing NSScrollView automatically and systematically jumps to a new location (usually � origin.x + 10.0).

This is not the correct behavior, the x scroll amount (or the first visible column if you prefer) should be the same when the view is resized.

I tried to record the x scroll position during viewFrameDidChangeNotification to set it back on windowDidUpdate using NSScrollView horizontalScroller setFloatValue but while the value of the NSScroller changes, the NSScrollView doesn't update.

Any idea on how to correct this ?

----

Don't manipulate the controls that the user uses, that's silly. Instead, tell the scroll view itself to scroll to a position that you saved before.

----

Perhaps use an NSTextView subclass and override     - (NSRect)adjustScroll:(NSRect)proposedVisibleRect?  

Also, you might want to consider if it's worth the inconsistency of overriding the default.  It looks to me like the view attempts to scroll such that the insertion point is horizontally dead center.

