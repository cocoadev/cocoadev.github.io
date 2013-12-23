---
layout: page
title: ProblemWithNSTableViewCellsWhenApplicationIsNotInFocus
---



I have a NSTableView subclass where the     - (NSRect)frameOfCellAtColumn:(int)column row:(int)row  and     - (int)columnAtPoint:(NSPoint)aPoint methods are overridden to provide differently sized cells based on the type of data that is display within them. This works great, but as soon as my application is not in focus the widths of the cells revert to be the same width as laid out in Interface Builder. When I focus the app again, they immediately return to the correct width.

Any ideas on how I can fix this behavior? Thanks.

