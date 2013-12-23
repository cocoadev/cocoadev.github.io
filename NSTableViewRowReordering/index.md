---
layout: page
title: NSTableViewRowReordering
---

In my application I have a standard NSTableView with some NSTextFieldCells for displaying each column of data, and I can't for the life of me figure out how to allow these rows to be reordered vertically within the table.  I have done setVerticalMotionCanBeginDrag:YES in awakeFromNib, but it appears to do nothing.  What am I missing?  Thanks.

----

What you are asking for (I think) takes some effort. This article gives a nice overview of what needs to be done conceptually:
http://www.red-sweater.com/blog/274/a-moveable-beast

