---
layout: page
title: NSTableViewTrouble
---

I'm having trouble with an NSTableView.  Anyone have ideas?

...

When I fire loadURL, the table loads and displays the data just fine.  But if I click on the table or focus it in any way, the data disappears.  I've confirmed with NSLog/printf that both the count and the object for each row is correct, so what gives?  --Owen Anderson

Erm, nevermind.  I fixed it.  Somehow I'd managed to have two NSTableViews on top of each.

