---
layout: page
title: ContextualMenus
---



See also:
[Topic]

----

From the macosx-dev list:

    
Date: Wed, 25 Apr 2001 09:34:08 -0700
From: Chuck Pisula <chuck at apple.com>
Subject: Re: Context Menus in NSTableViews
To: Richard Schreyer <richards9 at mac.com>

On Monday, April 23, 2001, at 08:01 PM, Richard Schreyer wrote:

> I have a NSTableView, and I want to add a contextual menu to each of 
> the items within it.  Calling -setMenu: on the cell in the 
> willDisplayCell: datasource method doesn't appear to work.  Anyone have 
> any ideas?

Try doing a setMenu: on the table columns data cell.  The data cell you 
see during drawing may be a temporary cell.  So, do something like:  
column2 dataCell] setMenu: menuForCellsInCol2].

Note that, by default, [[NSTableColumn only has one cell which is shared 
by all rows in the column.  If you need each context menu to have 
different items in it, you may need to make a subclass of NSTableColumn, 
override -dataCellForRow: , and either returns a distinct cell, or 
properly update the menu of the shared cell.

-chuck

