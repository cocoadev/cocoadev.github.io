---
layout: page
title: DropLocationForPromisedFilesDrag
---



Hi all,

I'm using     tableView:namesOfPromisedFilesDroppedAtDestination:forDraggedRowsWithIndexes: in my table view. After returning an array of names I create the files at the specified location     [data writeToFile:path atomically:YES]; and     [data writeToURL:url atomically:YES];. The problem is that when I drag files to Finder they appear not under the mouse cursor where I did drop them, but aligned to other icons (it looks like if my desktop has an option "Snap to grid" always turned on, but in fact it hasn't).
When using other types of drag and drop and writing file data directly to pasteboard, then everything works fine and resulting files appears exactly at the drop location.

I'm totally frustrated, what am I doing wrong?

