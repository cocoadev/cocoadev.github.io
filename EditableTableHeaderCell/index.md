---
layout: page
title: EditableTableHeaderCell
---

Is there a way to make the header cells of an NSTableView editable? I want users to be able to edit them like any other table cell.

----

The "trivial" solution is to create a preferences window or sheet and use some text fields to set the header cell string values.

NSTableColumn has a method

    - (id) headerCell to return the header cell. Once you have that, send the header cell a     setStringValue message.

This is not the same thing as double-clicking the header cell to edit as you would in IB, but it's a start.
Just my two hundred thousand zlotys.

----

One possibility would be to catch double-clicks on the table header (possible with setDoubleAction:), and then do some trickery to set up a field editor in the right place and take it from there. It won't be a simple built-in solution, but it should be doable.

----
Or you could perhaps create your own (editable) header cell and call     -setHeaderCell: on the table column??? You'd probably still have to call     -setDoubleAction: and override the default action here, but it's just a cell. See NSCell for more info.

----
*Or just hide the header and make the first row of cells appear slightly different. The only problem is you lose a lot of functionality this way that you'd have to recode, like sorting and moving columns. --JediKnil*


----
Except that *that's what the header cell is for*!!! :-)


----

Anyone ever find a simple solution to this? --Boomer

