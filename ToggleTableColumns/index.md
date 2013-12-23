---
layout: page
title: ToggleTableColumns
---

I want to allow the user to customize which columns are visible in an General/NSTableView (similar to the View > Columns menu in Apple's Mail app). What is the best way to implement this? I do not see any way to hide/show an General/NSTableColumn, so I assume I need to add/remove the column from the table every time. I have all of my columns set up in Interface Builder; so when adding a column after it has been removed, do I need to recreate the column from scratch programmatically? Or is there a way to use the table column I created in Interface Builder? Also, how does this all work with enabling General/AutoSaving for table column attributes (width, column order, etc.)? Will I need to save it manually because some columns are removed which were in the initial Nib file? Anyone know of any example Obj-C code that does all this? Thanks!

-- General/RyanBates

I've done a few things that might at least let me help you, conceptually. My recommendation is to not set up your columns via IB ( e.g., make an empty table ), but rather do them procedurally; then you can query your settings to decide which to show and which to hide. It's not too hard, I did it easy enough with probably less than 20 LOC -- however column widths are hard since you might just need to eyeball it. As it regards auto-saving, you might just want to discard that and let it be worked into your code for deciding which columns to show. --General/ShamylZakariya


Check this for a clever, apparently simpler solution that allows you to set the columns up in IB, and then add/remove them programatically (without having to recreate them from scratch):
http://trinfinitysoftware.com/blog/?p=6
-- Fab

----
A full working example of this can be seen in the Creatures source code ( http://www.mikeash.com/software/creatures/Creatures-source.zip ) in the G<nowiki/>enomeListController.m file. It implements a context menu on the table header (like iTunes), show/hiding of columns, and persistence for everything.

The code is BSD-licensed, so you may use it at will.
