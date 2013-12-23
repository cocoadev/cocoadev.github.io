---
layout: page
title: CoreDataWithOrWithoutBindings
---



The app I'm currently working on will use CoreData. However, I will do some custom drawing within my views (NSOutlineView, NSTableView) and these views may/will contain items that may not be of the same data type. For example, the NSOutlineView will have a Leopard's Finder style and along with data from the CoreData model, it could/will contain other items such as filters and a trash. Obviously these items aren't related to the data model so I was wondering if that was still possible with bindings or I will have to use some glue code to implement the behavior I intend for the views?

----

I'm not entirely sure I think you may be able to do it this way:


*Make one abstract entity that everything will fit into
*Make your other entities (i.e. Filters, Trash) children of the abstract entity


This should make your NSArrayController/NSTreeController happy, in that you set it's entity to the parent in IB, and it can contain the various types then. And if need be, you should then be able to subclass NSTableColumn, and overriding: - (id)dataCellForRow:(int)row and all that jazz to let you do the custom stuff for each type.

I've only done something like this once, so I don't know if it is the best way, but it worked for my needs.

++ Falco

----

Thanks. The thing I should mention is that items such as filters and the trash are not items contained in the store. They are simply there to perform actions to another view or to drag items to them...

