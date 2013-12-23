---
layout: page
title: HowToTellIFTheUserClicksAwayFromVariousFieldsOnAWindow
---

OK, I have an on-screen "form" that is used as such:

1. a table controls which item is being examined (list of names and some other info in the table). To switch which set of data is displayed in other fields on the form, one selects a different name in the table.

2. some NSTextFields, one NSTextView, one NSMatrix (radio buttons) that display information related to the currently selected item in the table

(all of this data--the fields in the table included--are data from the custom object being displayed)

Currently, I've set my document up as the table's delegate and change which object's data is being displayed in the textfields, etc. whenever a click occurs in the table (delegate method tableViewSelectionDidChange).

What I want to be able to do now is save the current object's data whenever the user picks a different table row (actually, immediately before). I also want to make sure any data that is changed gets saved if the user clicks any of the many buttons on the form.

Since it would be OK to save the data any time any field is edited (clicking a different item in the table might not be the best solution as I describe below), the way I thought I would implement this is by assigning the document as the delegate for each of the NSTextFields, the NSTextView, and the NSMatrix. Then, I attempt to trap the controlTextDidEndEditing delegate method for the fields, and call my save data function each time this occurs.

This works except for the following:

1. NSTextView does not use this as a delegate method and therefore I cannot find a way to tell when the user has clicked outside of the text view and onto something else.

2. NSMatrix also does not use this method and I can't track when the user clicks away from the radio buttons and onto something else.

3. If the user changes some text in a text field, then clicks one of the action buttons on the page, it doesn't seem like the controlTextDidEndEditing event occurs. I can fix this by simply having each button call the save routine before executing its own function, but this seems really silly.

If I go back to having the data save only when the user clicks a different item in the table, one solution seems to be to write something (a variable) to track the "last" item picked in the table and when the table's tableViewSelectionDidChange event comes in, I save the current data in the "last" items object variables before clearing everything and getting ready for the newly selected item. I would still have to have each button run the save routine as well, but this should work. 

Unfortunately, there is only one reason why this might not work. The form itself is part of a tabbed container, and what might happen is the user clicks one of the other tabs before clicking a new item in the table. In this case, the data on-screen stays on screen and never gets "saved" into the variables. Well, not until the user comes back to the table and clicks another item.

Can anyone help me with this? 

I'm trying hard to read the Cocoa/Objective C documentation, but it doesn't really give one an idea of the best way to solve this kind of logistical problem.

Thanks again,
Kent!

----

Offtopic, but there's another Kent here? --KentSutherland

I guess we're living in a Kent State.

----

You can update your object's state in     -controlTextDidChange:(NSNotification *)aNotification for the NSTextField,     -textDidChange:(NSNotification *)aNotification for the NSTextView (both of these are delegate messages) and just do it in the radio button's action method.

----

Instead of using delegate methods, you may want observe the NSTextDidEndEditingNotification (for the NSTextViews) and NSControlTextDidEndEditingNotification (for the NSTextFields) with the object as nil so it will inform you if any text changes for any object. That way you won't have to link up each field individually.

    
[[NSNotificationCenter defaultCenter] addObserver:self
		selector:@selector(saveData)
		name:NSTextDidEndEditingNotification
		object:nil];


Hope that helps.

-- RyanBates

----

The *DidEndEditingNotifications are the same as the delegate messages (an object's delegate is automatically registered for notifications) so the same problem would crop up. The textDidChange methods are also available as notifications and should be used instead.

