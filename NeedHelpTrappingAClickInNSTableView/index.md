---
layout: page
title: NeedHelpTrappingAClickInNSTableView
---

I need to change some various data fields in other objects in a window whenever the user clicks any particular row in a tableview.

Does anyone have any sample code on how to trap the click even in the tableview?

Once I know the user has clicked, I think I can get the row # with selectedrow or clickedrow, can I not?

The key is to have trap the click and update the other objects (data fields) at that time, based on which row is selected.

Can anyone please help me with this?

Thanks,
Kent!

----

If you only need to be informed when the selected row changes (by far the most common case in my experience),  then you just need to implement the table view delegate method     -tableViewSelectionDidChange:.  There is also the method     tableView:shouldSelectRow: if you need to know before the row selection changes.  -- Bo

