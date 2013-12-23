---
layout: page
title: SetDataSource
---

I'm a C/C++ coder who is just starting to learn Objective-C.  I have run into a few snags with the memory management sytem: release, autorelease, and retain.  For example, I understand that some methods will retain an object that is passed in as a parameter and others will not.

Does the method setDataSource: of NSTableView retain its parameter?  For example, what is the correct syntax for adding a new empty array as the data source?  This is what I have, but I don't know if it is correct:
    
[myTableView setDataSource:[NSMutableArray array]];


Does the array returned by [NSMutableArray array] have a retain count of 1?  Is that ok?  Is this programatically stupid???

*it's an autoreleased array. it will be released at the end of the current run loop.*

One other question, do I need to retain/autorelease with this call?
    
[myTableView setDataSource:[NSMutableArray arrayWithObject:someDictionary]]


*you would need to retain it, but see below...*

Thanks for any help, JoeCrow.

----

No, a tableview will not retain its data source - it doesn't own it, it just asks it for data to display. You can't use an array as a data source for a table view anyway (unless you've added the NSTableDataSource protocol methods as a category on NSArray.) A NSTableView's dataSource can be any class that implements the required methods from the NSTableDataSource protocol. You would typically have the data source be a NSWindowController subclass that is acting as FilesOwner for the table view's nib if you're loading the table view in a separate nib, or else you could use your NSDocument class or app delegate. 

----
What if I'm using a "Table Delegate"?

----

A table's delegate has no real effect on its data source - they're often the same object, but mostly for convenience or it "just makes sense" to do it that way.

