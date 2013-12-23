---
layout: page
title: NSTableViewSeparator
---

Does anyone know how to get a separator line between two rows in an NSTableView?

I mean one that looks good, ie. with proper padding etc.


----

Add an extra dummy row and use the table view delegate method heightOfRow to adjust the height, then draw a line in a subclass of NSTableView.

----

That is, if you only want 10.4 compatibility.

