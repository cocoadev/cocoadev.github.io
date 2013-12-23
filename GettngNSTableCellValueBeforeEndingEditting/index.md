---
layout: page
title: GettngNSTableCellValueBeforeEndingEditting
---

I have a user interface with a NSTable and a button. Users can edit some fields on the table and when the button is pressed, the program gets the values of the NSTable's cells and makes some calculations using them. But the user could press the button before having ended the editing in a cell, I mean without clicking outside the cell. In this case I want to get the value he was writing, but I get the value written before.
How can I get the last value?

----

You could use CocoaBindings to update the values stored in your model, or register for a notification that your cell class sends when its contents change (if these are text field cells you could use NSControlTextDidChangeNotification)

----

The problem is that if I use the method stringValue (or any xxxValue) I get the previous value. What I need is a method which gives me the new value. I don�t know where the text is being stored while the user is writing it; maybe knowing it would let me get the value that's being written.

----

The new value is stored in the field editor, which the table view cell is using to do the actual edit.

But I think you should look into simply sending     commitEditing to the controller responsible for the table view data, cause the other is really implementation defined and full of subtle details.

--AllanOdgaard

----

Send     endEditingFor:nil to the table views window.

