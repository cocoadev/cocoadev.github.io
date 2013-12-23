---
layout: page
title: NSTableViewHeaderMerge
---

I am trying to merge three column headers into one within a NSTableView. The three columns contain cells of type NSButtonCell and a size of 14x14. If would like to show a common header for the three columns with the title "Actions". I have been reading documentation about NSTableHeaderCell, but I really do not know where to start. Can anyone help?

----
Customizing a table view in this way is probably easier done by creating your own subclass of NSCell and using it in a single column. I've never heard of anyone actually combining table headers into one, and NSTableHeaderCell is rarely used directly anyway.
----

----
Would this NSCell have three NSButton inside?

Thanks for your help.
----
You'd probably find it easier to use an NSMatrix of three NSButtonCells.

