---
layout: page
title: CannotSaveChangesInSecondManagedObjectContext
---



Hello, I'm just getting starting with Core Data, so this may be a silly question...

I'm trying to create a second managed object context, using the same persistent store coordinator, e.g.:

    
[ ManagedObjectContext provided by NSPersistentDocument]            [ My own ManagedObjectContext ]
                                      |                                                             |
                                      |                                                             |
                                     [ Persistent Store Coordinator provided by NSPersistentDocument ]


I seem to be able to accomplish this using the following code:
     
	//Get the persistent store coordinator of the current document
	newContext = [[NSManagedObjectContext alloc] init];
	NSPersistentStoreCoordinator *coordinator = self managedObjectContext] persistentStoreCoordinator];
	[newContext setPersistentStoreCoordinator:coordinator];

	// Add an object to the new context
	[[NSManagedObject *newEntity = [NSEntityDescription insertNewObjectForEntityForName:@"Entity" newContext]; 
	[newEntity setValue:@"Value" forKey:@"value"]; 


The problem is that this second context does not seem to be saved (everything in the first context is saved properly).
So I tried to save the second context explicitly, using the save: method.  However, I got an error message saying that 
"This NSPersistentStoreCoordinator has no persistent stores.  It cannot perform a save operation"  But I was able to save
everything in the first context...

How can I save the objects that are in the second context?

Thanks for any help.

----

Take a look at MultipleStoresInCoreDataDocumentApp

----

Thanks for the suggestion.  It gave me some ideas.  I've now managed to get the NSPersistentStoreCoordinator to save the two contexts using [newContext save:nil].  I don't know why this did not work before...

