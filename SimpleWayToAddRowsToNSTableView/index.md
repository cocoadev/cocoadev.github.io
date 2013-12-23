---
layout: page
title: SimpleWayToAddRowsToNSTableView
---

Is there any way to add rows to a listview without tons of overhead (i.e. NSArrays and NSDictionaries) for "demo data" in a simple, non-functional interface demo? I can use NSDictionaries and the like but would like to avoid doing so since the project I'm working on is nothing but a mere non-functional interface demo -

----

Simple answer: CocoaBindings. Simple answer for anything prior to Panther (10.3): No, you're S.O.L.

With bindings, you can use Interface Builder to create an NSArrayController, drag a table view to the window, and bind each column in the table to its arrangedObjects. Drag an Add and Delete button to the window and connect them to the array controller's add: and remove: outlets. Run it, click the add button and a (blank) row is created for you to edit. Lather. Rinse. Repeat.

