---
layout: page
title: SelectingAndClickingATable
---

when a user single-clicks the row in the table, an action is invoked.  elsewhere, i have     [tableView selectRowIndexes:[NSIndexSet indexSetWithIndex:variable] byExtendingSelection:NO];, which selects the appropriate row.  however, I want to invoke that same single-click action.  how do i do that?

----
I would guess     tableView target] performSelector:[tableView action] withObject:tableView]; This is the usual way that actions are sent by subclasses of [[NSView. --JediKnil

