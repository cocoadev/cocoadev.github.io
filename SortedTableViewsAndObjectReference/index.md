---
layout: page
title: SortedTableViewsAndObjectReference
---

I have an application that makes use of Cocoa Bindings. A NSArrayController is the dataSource for a NSTableView and the array contains a list of custom objects I created. I'm successfully able to populate the TableView with new objects, delete and reorder them using sorting. Right now, I want to be able to double-click one of those objects and open a sheet where I'll be able to see further information about the selected object.

The question is, is there any way to keep a link between tableView's selected row and the actual index of the object at the arrayController, now that sorting changed the order in which the objects are organized on the tableView?

