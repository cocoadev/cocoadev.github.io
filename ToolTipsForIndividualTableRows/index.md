---
layout: page
title: ToolTipsForIndividualTableRows
---



Is it possible to display a different tooltip for each row in an General/NSTableView? I've got an General/NSTableView with a bunch of rows (700) and some of the entries are fairly long - too wide for the General/NSTableView, and making the view wider isn't an option. I'd like to have it so that hovering over a row displays a tooltip containing the full contents of that row. How would I go about doing that?

--General/OwenYamauchi

----

Use General/AMToolTipTableView, from General/ObjectLibrary

----

Yay Tiger now makes this easier!     tableView:toolTipForCell:rect:tableColumn:row:mouseLocation:

----

Has anyone noticed that the tooltip behaviour is inconsistent? There seem to be other reports of similar behaviour, so I imagine it is not limited to my experience. In particular, sometimes the tooltips are "hard" to display. Highlighting an entry in the table, then deselecting it, leaving the mouse stationary longer than normally necessary, clicking a few times, all seem to affect whether the tooltip gets shown. Sometimes it seems impossible to get the tooltip to appear, but once it appears the first time it becomes easy.
