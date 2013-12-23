---
layout: page
title: PersistentStore
---

A data storage location used by the CoreData framework's PersistentStoreCoordinator. The PersistentStoreCoordinator can add and remove PersistentStore instances on the fly, giving the appearance of a single storage location.

There is no official public PersistentStore class in CoreData 1.0, so the PersistentStoreCoordinator uses the id type to represent it.

Each PersistentStore has a store type, such as NSXMLStoreType (the default), NSSQLiteStoreType, NSBinaryStoryType or NSInMemoryStoreType. The initial release of CoreData does not support custom store types. The NSSQLiteStoreType has some scalability advantages over the other types, since it supports faulting and incremental writes. By contrast, the XML and Binary stores types read and write the entire object graph at each load or save event.

More information can be found here:
http://cocoadevcentral.com/articles/000086.php#10

