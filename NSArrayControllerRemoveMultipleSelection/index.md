---
layout: page
title: NSArrayControllerRemoveMultipleSelection
---

Hey all,

I've run into a very strange issue, and I was wondering if anyone might have run into it before or perhaps know the cause.

I have a delete button hooked up to the Remove: method of my NSArrayController (which displays its contents in an NSTableView that can have multiple selections).

On 10.4, whenever I press the delete button all of my selected objects are removed.  However, I was testing my app in 10.3, and I noticed that pressing the delete button only removes the selected objects if only 1 object is selected.  If multiple objects are selected, it does nothing!

Does anyone have any thoughts as to what might be going on here??

