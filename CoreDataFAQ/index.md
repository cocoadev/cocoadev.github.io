---
layout: page
title: CoreDataFAQ
---



Frequently asked CoreData questions. See also, CoreDataQuestions

----

*How do I access a CoreDataEntity?*

Once you have an entity, you can access related entities through KeyValueCoding. But to get that first entity is tricky. Apple recommends you create a KVC accessor in your NSPersistentDocument sub-class. This accessor must get an NSManagedObjectContext and create an NSFetchRequest to retrieve a root entity. See http://developer.apple.com/documentation/Cocoa/Conceptual/NSPersistentDocumentTutorial/04_Department/chapter_5_section_3.html. 


Please properly distinguish between the terms "entity" and "instance".  You can access an entity from a managed object model (-entitiesByName), from a managed object (-entity), and from a managed object context (+entityForName:inManagedObjectContext:).

You access instances of an entity in a variety of ways.  Apple does not simply recommend that "you create a KVC accessor in your NSPersistentDocument sub-class".  If you want to traverse relationships, you can do so using key-value coding.  You can also fetch objects directly by executing a fetch request (see http://developer.apple.com/documentation/Cocoa/Conceptual/CoreData/Articles/cdFetching.html).  If you're using Cocoa Bindings, you may also use a controller object to manage the managed objects and, if necessary, access the controller's contents (see http://developer.apple.com/documentation/Cocoa/Conceptual/NSPersistentDocumentTutorial/04_Department/chapter_5_section_5.html).

