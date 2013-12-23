---
layout: page
title: NSTableViewDataSource
---

[http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/ObjC_classic/Protocols/NSTableDataSource.html]

Informal protocol implemented by objects acting as a data source for an NSTableView to provide the table view with data to display. There are two methods that you **must** implement, and a few more optional methods that can be used to customize your table view's behavior.

The required two:

----

    - (NSI<nowiki/>nteger)numberOfRowsInTableView:(NSTableView *)aTableView

*Returns the number of records managed for aTableView by the data source object. An NSTableView uses this method to determine how many rows it should create and display* You typically return     [myTableViewArray count] here.

----

    - (id)tableView:(NSTableView *)aTableView objectValueForTableColumn:(NSTableColumn *)aTableColumn row:(NSI<nowiki/>nteger)rowIndex

*Returns an attribute value for the record in aTableView at rowIndex. aTableColumn contains the identifier for the attribute, which you get by using NSTableColumn�s identifier method.* A standard format is to store table view data as an NSArray of NSDictionaries, with the dictionary keys being the same as the identifiers for your table columns. So you'd return     myTableViewArray objectAtIndex: rowIndex] objectForKey:[aTableColumn identifier here.

----

**
NOTE CAREFULLY the advantages of using KeyValueCoding in accessing the ivars in the data source; this will go a long way toward making your table access methods crisp and clean
**

see also NSTableViewTutorial

To handle your table data with far less "glue" code, learn how to use CocoaBindings (for which, see the excellent tutorial at:

http://www.cocoadevcentral.com/articles/000080.php

