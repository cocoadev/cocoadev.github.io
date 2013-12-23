---
layout: page
title: BindingsIntrospection
---

Q: How do you take an object, find out what it's bound to, and retrieve values for the bound properties?

A: There is no documented, supported method.

A2:  I'd like to start a discussion about this.

----

Disclaimer:  unsupported means unsafe.  This topic is for exploration, but real world use is not a good idea.  Hopefully Apple will release public methods in 10.4.

See also BindingsBehindTheScenes. 

----

In an NSTableView subclass of mine, it would be nice to be able to take a look at the table data.  Without bindings, this is just     -[NSTableDataSource tableView:objectValueForTableColumn:row:].  With bindings..

Well, in 10.3.7 this seems to work.  
    
[[[*tableColumn* _bindingAdaptor] referenceBinder] 
    valueForBinding:@"value" atIndex:*i* resolveMarkersToPlaceholders:YES]

