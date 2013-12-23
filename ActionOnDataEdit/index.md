---
layout: page
title: ActionOnDataEdit
---

Is there an easy way to get an action to run when a user edits data in a table view, specifically controlled by NSArrayController (a sublass, actually) and CoreData for the storing. When a new entry is created, a table column in another tableview is created as well. I want the table column's identifier and title to change when the first tableview's entry's text is changed. I'm using Key-Value to name the table column, and it works fine, but I need it to change when the entry is changed. Is there a way to get NSArrayController to send a delegate message or something? I looked at subclassing it but couldn't find a method that does what I want. Any help is greatly appreciated.

----

Sure. Add your controller (the one that would handle creating the new column in the other table) as an observer for the relevant key or key path for your data model. Read up on KVO (KeyValueObserving). This is basic knowledge you should have if using bindings and Core Data, so read the documentation on KVO very thoroughly.

