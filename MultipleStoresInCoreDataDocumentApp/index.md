---
layout: page
title: MultipleStoresInCoreDataDocumentApp
---



Hi all.

I have a Core Data document based app, it's very simple at the moment.

At first, my App (as with any app based on the XCode Core Data "templates") had save and load functionality built right in for me. This is good.

However, I have need for an in-memory store for certain things that don't need to be saved to disk. I get my in-memory store set up like so:

    
// In "windowControllerDidLoadNib" in 'MyDocument'
{
	NSPersistentStoreCoordinator *documentStoreCoordinator = self managedObjectContext] persistentStoreCoordinator];
	[[NSError *error;
	
	id inMemoryStore = [documentStoreCoordinator addPersistentStoreWithType:NSInMemoryStoreType configuration:nil URL:nil options:nil error:&error];
	if (inMemoryStore == nil) 
		NSLog(@"In-Mem Store Configuration Failure\n%@", ([error localizedDescription] != nil) ? [error localizedDescription] : @"Unknown Error");
	else
		{
		// New store has been succesfully configured.
		}
}


This works perfectly, when I want to create a managed object that'll go into this in-memory-store I create the object with     [aController newObject;] then add that object to the controller with     [aController addObject:theNewObject]; and then assign it to the in-memory-store with     self managedObjectContext] assignObject:theNewObject toPersistentStore:inMemoryStore];.

The in-memory-store seems to work perfectly in this manner.

However, with this in place, when I save a document in my application - none of the data is actually saved. And so I currently am of the belief that in creating a new persistant store (in-Memory-Store) my Core Data app is now assigning *everything* to this store, and so, nothing is being saved.

So, with this in my mind, I thought I should call     assignObject:toPersistentStore on everything managedObject that I create, making sure that it gets sent to the right store. So, I have tried to find the 'auto' persistant store that Core Data uses by default for your objects when you create a fresh Document Based Core Data application - but am suprised to find that     [[documentStoreCoordinator persistentStores] count]; returns only '1' - my in-memory-store.

So - what happened to the 'default' store that Core Data uses to save your stuff if you don't monkey with the stores? Did it get replaced by my inMemoryStore - and therefore do I need to manually create another store for the stuff I *do* want to save to disk?

Thanks for any help you may be able to offer - apologies for the somewhat long-windedness of this post - I've tried to explain as clearly as possible.

Thanks again in advance.

(Pete).

----
Don't know much about [[CoreData, but it looks like you could just add another persistent store (before or after the in-memory-store, don't know which), and have that one used as the new default. It still won't give you the original store, but it should work fine, and it will let you choose what format to save in. --JediKnil
----

Thanks, that's very much what I intend to do. Nevertheless, I'm keen to keep everything neat, and just wondering if there was something I'd missed, before bolting on another persistent store.

----

By managing my own store that I use for stuff I save to disk, I'm not sure where to put it (which file URL to supply to     addPersistentStoreWithType:... ... URL:.

At the moment I have it going to the Application Support directory. But because my app is document based I need to ensure that each file URL for each document's store is unique, so that's a load of logic I don't want to write if there's an automated way to do this... is there?

Also I'm going to have to clear up the file on each close of a document.

The more I think about it, the less it seems I should be using a store in this way, but should in fact use another inMemoryStore that is saved to disk (when the user chooses "Save" etc.). Is this the way things are set up automatically in an untouched "Core Data Document Based Application" XCode template project?

Thanks.

----

I think the persistent store which is used by NSPersistentDocument gets created when it is saved.
Try opening/reverting an existing document - then count will return 2 and it will save your changes.
Seems like NSPersistentDocument just uses the first available store if there is one and
stops allocating its own.

Should this start a new life as a bugreport or do we miss something ?

Update: 
In my case (where the data is read-only after the objects are created), I tried to 

    
[documentStoreCoordinator removePersistentStore:inMemoryStore error:nil];


after constructing my object graph - which solves the problem with new documents not using a real store.

BjoernKriews

