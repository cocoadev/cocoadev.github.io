---
layout: page
title: SortingInTableViewWithCoreData
---

I need to sort a Tableview holding an array of CoreData objects...
Its easy to do with bindings but I need to sort the objects lika in iTunes & iPhoto
Im trying to use a "Library" object at the top and then 2-3 other objects after it at the top at all times.
I just  can't seem to get it right :( 

Have anyone tried to implement this or have some ideas?
I belive I have to subclass NSArrayController... :)

----

If you don't need to sort ahead of time, just let the user click the column header; sorting is automagic. If you want to sort ahead of time, set the array controller's sort descriptors (look up NSSortDescriptor and setSortDescriptor).

