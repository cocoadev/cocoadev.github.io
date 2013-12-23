---
layout: page
title: DisablingWrapInTextView
---



Is there an easy way to prevent an NSTextView from wrapping it's contents... so it shows the horizontal scrollbar?

----
You could try setting its springs in IB to stretch horizontally, but keep the containing scroll view on a fixed-width.
----

Thanks, but I need to have the scroll view scale with the window. :)

----
See file:///Developer/Examples/AppKit/TextSizingExample
----

----

Here is a simple way to disable wrapping in an existing NSTextView:

    
const float LargeNumberForText = 1.0e7;

NSScrollView *scrollView = [textView enclosingScrollView];
[scrollView setHasVerticalScroller:YES];
[scrollView setHasHorizontalScroller:YES];
[scrollView setAutoresizingMask:(NSViewWidthSizable | NSViewHeightSizable)];

NSTextContainer *textContainer = [textView textContainer];
[textContainer setContainerSize:NSMakeSize(LargeNumberForText, LargeNumberForText)];
[textContainer setWidthTracksTextView:NO];
[textContainer setHeightTracksTextView:NO];

[textView setMaxSize:NSMakeSize(LargeNumberForText, LargeNumberForText)];
[textView setHorizontallyResizable:YES];
[textView setVerticallyResizable:YES];
[textView setAutoresizingMask:NSViewNotSizable];

