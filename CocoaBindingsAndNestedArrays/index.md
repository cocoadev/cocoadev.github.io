---
layout: page
title: CocoaBindingsAndNestedArrays
---

Hello, I've been working in a app that uses Cocoa Bindings (not Core Data). The GUI is composed of two NSTableViews and a series of textFields. The first TableView allows the selection of an object from a list, and the remaining interface elements allow editing the information for the selected item.

An NSArrayController controls the content that is presented by the first NSTableView; the content is basically an array of custom objects (ex: Employee); each employee has many properties, most of them strings. But one of them is a NSMutableArray which has another group of objects.

Binding each of the string items to the GUI has been easy. For each textField, I successfully bound their values to the NSArrayController and the respective property. But I still could not figure out how to link the second NSTableView to each object's NSMutableArray.

Ideas?

----

Just create another NSArrayController and bind it to the array of the selection of the first controller.  You can then bind values in the second table to that.  Something like:

First controller: Employee, bind to File's owner.employees.  Second controller: NSMutableArray, bind to First controller.selection.array.

----

Worked as a charm. Thank you!!

