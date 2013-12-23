---
layout: page
title: CustomEditCells
---

Did anyone manage to write their own NSCell subclass for use in NSTableViews, which support editing (and not just display)?

I have several problems:

1) it seems I need to instantiate my cell with "initTextCell:", even though it's derived from NSCell (not NSTextFieldCell),

2) it seems that editWithFrame:inView:editor:delegate:event: is only invoked when the editor is activated through mouse clicks -- if I tab to the column using my cell, it will just re-use the previous cell,

3) even though my cell keeps an 'object' which represent my value, tableView:setObjectValue:forTableColumn:row: is invoked with an NSString representing the value (the same string I use for display purposes),

I assume there will be dozen more problems when/if I solve these (e.g. the frame is not drawn) -- so it would really be nice if someone either have source code implementing a custom cell editor or can point me toward a tutorial discussing all the caveats of NSCell subclassing.

----

If you want to do text editing and have the result stored as a custom object, I'll bet dimes to dollars that you'll be happier using a custom formatter than an NSCell subclass.   Just subclass NSFormatter and implement -stringForObjectValue: to return a display string for your custom object and -getObject:forString:errorDescription: to create an instance of your custom object from a given text input.  There are some other more advanced methods for partial string validation and displaying attributed strings, but those two will get you started.  -- Bo

----

The thing I want is to show key bindings in a column of its own. When activated (for editing) the users keystrokes should immediately be converted to the key in question, similar to Project Builder.

So an NSFormatter doesn't really work, because it doesn't know about F1, Delete, Page Up or similar.

----

Ok, I'm catching up from HidingObjectInNSCell now. I have a little example running on my machine where I've replaced the normal text cell w/ a trivial custom one. Basically:

1) Yes. initTextCell is required. I also had to say "setEditable:YES"

2) Didn't check.

3) Yes, the NSTableDataSource protocol specifies the data source and table view exchange information as "id"s, not as NSCell wrappers. You could imagine a data source API that used NSCells as the point of information exchange, but that's not how NSTableView works. 

4) I had no trouble with drawing, etc., since my trivial cell was actually an NSTextFieldCell subclass. I strongly suggest working with an NSTextFieldCell subclass so you can get the most leverage out of the AppKit. 

5) Regarding the problem at hand, reproducing the UI from PB's key bindings pref pane, keep in mind PB is using a NSTextFieldCell subclass to display and receive text. I don't quite understand what it's doing in its subclass, but I'm fairly certain a custom formatter is not part of the solution here. they're just shuttling text normally through the  NSTableDataSource protocol.

So I'm sorry I don't have definitive answers for you, but I think I can get you on the right track here. 

-- MikeTrent

----

I initially did use an NSTextFieldCell, but then I could not overload the editing behaviour, as it would only invoke textDidChange:, but not textDidBeginEditing: or editWithFrame:inView:editor:delegate:event:, which I need to setup my NSApp sendEvent hook (in order to get all keys).

The rendering is however correct when using an NSTextFieldCell.

With respect to #3, I did not mean to say that NSCell's should be sent to the data source, only that when my NSCell does a [self setObjectValue:someObj], it should be this object ('someObj') which is given to the data source, not the string from the field editor -- the only way I can avoid this is however, by adding an NSFormatter, but then the system will just ask my formatter to convert the string from the field editor, and give the result to my data source, so the original setObjectValue: is still ignored.

----

Instead of implementing a "sendEvent hook", have you considered simply using [NSApp currentEvent] to get the current event out of context? If all you're doing is saving the NSEvent object, NSApplication is already doing that for you ... -- MikeTrent

----

From where should I call this method? I could do it in didChange, but then I'd not get most of the special keys.

