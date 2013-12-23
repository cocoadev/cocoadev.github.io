---
layout: page
title: CustomTextFieldCellEditor
---

Hey, I have an NSTableView and a subclass of NSTextFieldCell used as the table's main column's data cell.

The rows in the table are slightly large, and I would like the field editor to be neatly centered in the row when double clicking for editing.  I would also like to change the color of the text inside the field editor.  How can I modify the field editor for the cell so that I can specify a custom frame and text color?  I have tried using setupFieldEditorAttributes -- but have had little success. Any ideas?  Thanks.

----

Override the cell editing methods editWithFrame:... and selectWithFrame: ... and modify the passed rectangle before calling thru super... after the super call, you can also mess with the text object that's passed as an argument to adjust text color.

----

Works like a charm, thanks.

