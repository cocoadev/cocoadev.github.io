---
layout: page
title: NSCellINNSTableViewIsssues
---

Hello,
I have created a subclass of NSActionCell, with two textfields above each other and an image placed to the left. I have also overridden the -(NSSize) cellSize and cellSizeForBounds:(NSRect)arect methods, yet when placed in a NSTableView, the rows are vertically truncated and not showing the full height for the row.

Also, i'm not sure whether I should edited one of the NSTableView pages, or NSCell pages, so I should do that, i can edit those and remove the suggestions from this.
Thanks for your time.
-SSG

----

The rowHeight of a table view is not computed by asking the cell for its best size, it's a property of the table.

[NSTableView setRowHeight:]

There's also a delegate method if you need variable row height.

----

For completeness sake, this delegate method is called     -tableView:heightOfRow:, and is available on 10.4+.
----
Thanks, I was quite surprised by that and used setRowHeight in my investigations - thought it was hack on my part but then noticed experienced programmers doing the same thing.
Thanks!

