---
layout: page
title: SortingNSValueTransformerBoundNSTableColumns
---

This might also be called : Sorting Core Data-based Table by to-many relationships.

I was having problems sorting an NSTableView bound to an NSArrayController (also an NSOutlineView/NSTreeController issue).

Certain columns are bound to to-many relationships. An NSValueTransformer subclass translates the related set into displayable text, such as a count of the related entities, or the name of the first related entity (NSSet's dont have a "first", but I have an "order" attribute in the related entity for that).

One of the problems: The columns that use NSValueTransformer subclasses don't sort, with logged "*** -[_NSFaultingMutableSet compare:]: selector not recognized". 

----

Solution: add a special accessor-like method to the class being displayed in the table (an NSManagedObject subclass). The accessor, like "-employeeCount", returns a number or string using the same algorithm as the NSValueTransformer subclass uses to display the value (again, based on the to-many relationship--so the first thing it does is call [self valueForKey:@"employees"] returning an NSSet). Then, the NSTableColumn 's sort key is set to employeeCount in IB. Done.

The code implementing the algorithm could be shared instead of copied, by putting it in a class method (or even instance method) of the NSValueTransformer subclass. --PaulCollins

