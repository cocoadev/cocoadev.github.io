---
layout: page
title: RemovingNSButtonCell
---

I've got my table setup so one column is all NSButtonCells. However, I'd like for some of the rows to not show the cell, but just show blank. Is there an easy way to do this, or do I need to create a custom cell that internally has its own NSButtonCell and draws it only if     [self drawButton] (or something like that) is true? I tried using     setType: with NSNullCellType but that didn't work.

----
You could     return @""; from     tableView:objectValueForTableColumn:row:...?

Alternatively, you might     setTransparent:YES from     tableView:willDisplayCell:forTableColumn:row: and then check     isTransparent when the action gets triggered.

