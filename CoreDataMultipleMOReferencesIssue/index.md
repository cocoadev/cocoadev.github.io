---
layout: page
title: CoreDataMultipleMOReferencesIssue
---

In Brief: When saving a core data app, an object which appears on the receiving end of multiple relationships will have all of those relationships broken except one.

Synopsis:
I have a project in which a pool of managed objects is created.  Those objects can be added to what we're going to call "playlists" (just for argument's sake).  This works completely as expected, with the same object appearing in two or three different lists, until the program is closed (so there's something going on in the validation and saving phase).  What happens is that when opening the project, while all playlists are intact and all items are intact, the items are now exclusive to one apparently random playlist.  That is, though the items have no relationships specified, the playlist relationship is getting broken in the save process.

I am not convinced that this is not a bug, but my inclination is to accept that the cause of this problem is UE until others can confirm that this is incorrect behavior.  To that end, I am sticking this VERY small project up so you can test for yourself and see what's going on. 

http://idisk.mac.com/omnius/Public/CDMultipleReferencesTest.zip

The process in more detailed terms is nonetheless very simple. In a vanilla core data app, define the following data model:

http://idisk.mac.com/omnius/Public/CDMRTModel.jpg

the names are strings, everything else is vanilla or WYSIWYG. Then, set up in IB the following arrangement:

http://idisk.mac.com/omnius/Public/CDMRTScreen.jpg

the tableviews are bound to two NSArrayControllers.  Both have their managedObjectContext bound to the delegate.  The first has its entity set to "list" and automatcallypreparescontent.  The second has its *contentSet* bound to the first's "selection" controller key and "name" keypath.

The buttons each link to the only added methods of the entire project:
    
- (IBAction)newList:(id)sender;
- (IBAction)newItem:(id)sender;

- (IBAction)newList:(id)sender
{
	//Insert a new list into the hierarchy
	NSManagedObject *newList = [NSEntityDescription insertNewObjectForEntityForName:@"list" inManagedObjectContext:[self managedObjectContext]];
	[newList setValue:@"new list" forKey:@"name"];
	
	//Add all items to the new list
	NSFetchRequest *fetchRequest = [[NSFetchRequest alloc] init];
	[fetchRequest setEntity:[NSEntityDescription entityForName:@"item" inManagedObjectContext:[self managedObjectContext]]];
	
	newList mutableSetValueForKey:@"items"] addObjectsFromArray:[[self managedObjectContext] executeFetchRequest:fetchRequest error:nil;
}

- (IBAction)newItem:(id)sender
{
	//Insert a new item into the context
	NSManagedObject *newItem = [NSEntityDescription insertNewObjectForEntityForName:@"item" inManagedObjectContext:[self managedObjectContext]];
	[newItem setValue:@"new item" forKey:@"name"];
}



What happens is, upon clicking the item button a new item is created and added to the context.  Upon clicking the list button, a new list is created and added tot eh context, and the list's "item" relationship is filled with ALL existing items.  You can confirm this works by downloading and building the project, the clicking one and then the other button a few times.  If you then just navigate the list tableview, you will see that each list has steadily more and more items included in it, which is of course what we expect.
The problem is that if you then quit, when you reopen the app now *only the last list has any items*.  This is definitely not the expected behavior.

CONFIRMED AS NOT THE PROBLEM:

My personal suspicion was that I was improperly making the relationship programmatically, but it is direct from the docs.  

I have also confirmed that the URIRepresentation and managedObjectID is in fact unique.  This occurs with objects created during the current session as well as items created and saved previously.

I am beginning to wonder if this has to do with the archiving procedure for object graphs, where I've read that an archiver includes a given object only once for each graph.  This makes sense, but I don't see anywhere written that this process is part of the Persistence scheme that Core Data uses, nor do I see that that would make any sense since again the method used for creating multiple relationships is the very same recommended by apple in the docs.  Quote
    
NSMutableSet *employees = [aDepartment mutableSetValueForKey:@"employees"];
[employees addObject:aNewEmployee];


----

The problem is that you don't have an inverse relationship.  You should always create an inverse relationship.

----

Thanks, I have considered that, but have been hoping that that was not the only solution.  Maintaining a many-to-many relationship list, when fully one-half of the relationship is never, ever referred to, seems like overkill and it complicates the necessary code as well.  Do you think that every song in itunes knows every playlist to which it belongs?

Well, I will modify the sample and see if that solves the problem.  In the meantime, I think that if you absolutely must have an inverse to preserve the relationship, then that should be enforced in the modelling phase for any attribute not marked as transient.  That Core Data feels at liberty to delete non-transient relationships the program has specified very much violates principles of continuity, to me anyway.  Thanks, and I definitely appreciate any further input, especially if it means I won't have to file a bug report.


----
(Newcomer in the thread). I agree with the OP that if this is really happening, this is not expected behavior and that would count as a bug. I just have 2 comments to make:


* Maybe the behavior will be different for different backend formats. By default, CoreData uses xml I believe. The OP mentions the problem, and it would probably be more likely to happen with xml. But I agree that if this is the case, it is not good behavior.
* Maintaining a many-to-many relationship list DOES NOT complicate the code at all. CoreData will maintain it automatically, so that if you set a relationship, the reverse relationship is also automatically taken care of.


----
I can confirm the same problem in both SQLite and XML.  The suggestion of creating an inverse works, but only so long as the loop is between two entities only. That is, if I have a Playlist entity, and then a smartPlaylist entity which inherits from playlist, the problem persists because CD is not yet smart enough to resolve that the relationship is actually just a straight inverse.  So, no, the main problem persists:  CD should not ever delete non-transient attributes during its save:.  Am I wrong?

----
Well, I think I may submit a bug report on this one, insofar as for me this is behavior which is not controllable and therefore incorrect.  However, I have solved the present case by following the poster's advice and creating an inverse relationship, which actually must be maintained manually.  Eg, every time I change one relationship I must change the inverse manually, as in:
    
		[[aListItem mutableSetValueForKey:@"items"] removeObject:anItem];
		[[anItem mutableSetValueForKey:@"lists"] removeObject:aListItem];

and so on.

