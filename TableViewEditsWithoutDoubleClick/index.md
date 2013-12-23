---
layout: page
title: TableViewEditsWithoutDoubleClick
---

I want to provide edit-in-place for a General/NSTableView, however, I do not want it on double click, since editing is the lessor of two desired actions.

The problem is twofold, first of all, if I want to trigger edit-in-place simply by clicking an already selected item, how can I do that? i.e. when the action is sent, the item is already selected, so I do not know if it was selected previously to the click.

My second problem is, that I need to disable editing for the General/NSTableColumn, otherwise the double click action is not sent. But this means that the user cannot tab between items (to move the edit field), and in fact, if the user presses return, the edit-field will stay on the item just edited, which is undesirable. What I would prefer was that return would finish the edit and remove the field editor, and tab would advance the field editor to the next item. How can this be done?

----

Thru experiments I have found thatI can call [self abortEditing] in an overloaded textDidEndEditing: (which still pass the message to super) -- however, this will make the table view loose focus -- I have tried several other alternatives, but all with undesired side effects. I really feel like playing with fire here :(

Also, I have no idea how the user is supposed to abort editing of an item -- imagine we have a long filename, the user accidentally hits a key while the entire text is marked, and boom it's gone for good, no undo and no abort...

With respect to triggering edit on the second single click then I have no idea -- maybe this is why Finder would go into edit mode on the first click? ;)

----
Rather than having the editing begin on click, perhaps have an edit button or context menu item which would do it. Also, General/NSTableView's     - (void)editColumn:(int)columnIndex row:(int)rowIndex withEvent:(General/NSEvent *)theEvent select:(BOOL)flag method may be of interest (I've never actually done anything with it, it's just in my notes for an upcoming project.)

----

Thanks for the suggestion, though I have many table views which must support this behaviour, so an edit button is not appropriate -- I already have a context menu, but I also want the single click feature as available in both Finder and iTunes... :)

----

My users complain to me about how it's my fault that the table views in my app act like General/NSTableViews and not Apple apps. I think it's pretty ridiculous that there's no solid information here or anywhere else on this seemingly vital and simple task. I want the drag and drop to act normal but allow single-click editing and double-click to send the action message. How hard can it be? --General/FrustratedCocoaer

Wow, over an entire month and no one offers even a tip. I've pulled my hair out over this one. There has to be an elegant solution. It seems like everything I come up with (i.e. involving General/NSTimers or performSelector:withObject:afterDelay: and some hackish way of selecting the contents of the cell) is either very buggy, or risky. Please, please, please... any help at all would be appreciated. --FC
