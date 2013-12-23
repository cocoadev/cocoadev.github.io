---
layout: page
title: PreventRowSelectionByDraggingAcrossTableView
---

I need a tableView that allows the user to select only 2 rows at a time.
I customized the selecting behavior of my table view with mouseDown: and tableView:shouldSelectRow: and it works just fine for when the user (shift)-(command)-clics on the rows.

The only problem is that when you click and drag, you can select all the rows you want to�

I tried to implement mouseDragged: but unsuccessfully.

Any help on this issue would be very appreciated!

Thanks.

--wdyp.

----

It looks like you also need to use     - (void)selectRowIndexes:(NSIndexSet *)indexes byExtendingSelection:(BOOL)extend

(BOOL)extend should be set to NO. It should prevent the user from extending the selection by dragging.

----

Thanks! I did notice this method but I don�t understand how to use it: it seems to be used to select some rows programmatically, but where do I short-circuit the select system to use this method as it is not a delegate method?

Thank you if you can explain this for me!

PS: the user can select more than 2 rows with shift-arrowUp/Down too and I�d like to avoid this too.

--wdyp

----

To "short-circuit" the select system, perhaps you could subclass NSTableView to override     selectRowIndexes:byExtendingSelection:, and use your custom subclass instead of NSTableView...

----

Thank you, it works!

With Shift-Arrow the result is perfect, while shift-dragging, the behavior is a little strange (it selects row 1 and 2, then if the user tries to extend the selection to row 3 rows 1 and 2 become deselected and row 3 selected) but the user can�t select more than 2 rows so it�s perfect.

Thanks again. :-D

-wdyp

