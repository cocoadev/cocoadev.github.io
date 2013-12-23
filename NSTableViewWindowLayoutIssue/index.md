---
layout: page
title: NSTableViewWindowLayoutIssue
---

I have a small visual issue with some of my table/outline views that I would like to resolve. I will cite one case, as they are all the same issue.

I have a single NSTableView that occupies the entire contents of a window. This was set up in IB.

Has anyone seen, and have insight on how to remove the following visual artifact that seems to manifest only during runtime?

http://homepage.mac.com/vannote/images/TableIssueOptimized.gif

Thanks

-- NeilVanNote

----

Change the table view's border style to none (the dotted outline) in IB.

----

Thanks, That did the trick. Didn't even think about it, as it was not showing in IB.

-- NeilVanNote

