---
layout: page
title: CoreDataAddingRelatedManagedObjects
---

I have a Core Data app that is working fine. Its data model has two entities: Department and Person. A Department has multiple Persons. Two table views, one for Departments and another one for the associated Persons. By using IB, Array Controllers and bindings, I can create Departments and associate multiple Persons to each of them. Perfect.

Now, I want to do the same thing, but programmatically. I'm able right now to insert a new Department (a managed object) into the managed object context and have the UI automatically show me the added item by using the code below on MyDocument.m (a subclass of NSPersistentDocument):

    

NSManagedObjectContext *moc = [self managedObjectContext];
NSManagedObjectModel *mom = [self managedObjectModel];
[[NSManagedObject alloc] initWithEntity:[[mom entitiesByName] objectForKey:@"Department"] insertIntoManagedObjectContext:moc];



The question is... how can I add a new Person instance to this newly created Department? Where are the hooks that connect one to the other?

