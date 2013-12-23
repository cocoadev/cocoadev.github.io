---
layout: page
title: NSManagedObjectModel
---

*Editor's Note: this page concatenates a series of posts by a single individual trying to learn how to use NSManagedObjectModel with SQL, and taking place essentially without feedback, or at least by minimal interaction with feedback that was offered. Hopefully some insight can be obtained by examining the narrative in one place. Some of the upshot is summarized in CoreDataMostWanted.* 

A class in the CoreData framework.

A data model outline for a CoreData application. Contains Entities, Attributes, and FetchRequests. Usually created using Xcode's visual data modeling tool, but can also be created programmatically using the NSManagedObjectModel class.

The Managed Object Model determines the structure of the data store, such as columns in a SQLite database. It also determines which class a given Entity is mapped to. A CoreData application usually contains at least one model but can have multiple models that ('can be' or 'are'?) merged at runtime.

CocoaDevCentral has some more information:
http://www.cocoadevcentral.com/articles/000086.php#3

----

**Maintenance**

How do I maintain the app and its  data over successive enhancements and upgrades?  In the past I have used my own proprietary database which is 5th Normal with basically only 4 tables (Object, Association, Property and Type. Plus of course an Audit Trail of changes with rollback functions.

Now I have recently redeveloped one of my apps in CoreData.

I have been through the Core Recipes example -  as soon as you let one of these apps loose on real users they want enhancements - insistently.

One idea is to destroy my new datamodel (that works) and replace it with a Managed Object Model that only has 4 tables and is 5th Normal - then I never have to change the model and the SQLite database is robust. That means I have to write a lot of Glue Code between my Controllers and the ManagedObjectContext If I go ahead and do this solution will Apple immediately upgrade CoreData so that maintenance issues arise and all my new glue code needs to be thrown away again.

I have tried using XMLStoreType, but my software needs to do frequent saves so XMLStore's  performance doesn't please me. SQLite works fine except for a few speed humps during batch load and batch reporting where it has to load most of the model data involving  zillions of fetches and unfaultings.

Maybe I should store the .mom file in the SQLite database itself, dump the data to 5th normal and then reload it into whatever is the current Managed Object Model. I know one can dump to XML and then relaod, I tried that but didn't really like it much (well not at all actually).
Every EntityType in my current CoreData dataModels contains an AttributeType called "extraData" of type Binary (NSData / BLOB).   When I need to quickly add new AttributeTypes to an existing model without causing a data-dump and reload I simply write some glue code to store the new attribute values in a NSDictionary and archive it into the extraData field.  Later on I add the appropriate AttributeType to the dataModel and then migrate from the old to the new dataModel. For iFamily_For_Tiger migration can be simply a matter of generating a GEDCOM file from the old version of the application and then reloading it with the new version.  Of course, with a 5th normal database there would be no need to include the "extraData" field. 
 
Tomorrow I'll try archiving the mom file into the store's metadata dictionary and saving the mom file in the persistent store. Then to upgrade from one version of the model to another I will try to do a generic dump staring with [moc enitities] plus their attributes, properties and relationships.  Then I need to write a (generic) reload into the new ManagedObjectContext. Of course, I still have to know how to handle the exceptions but the bulk of will already be done. I'll give it a go unless someone has a better idea.

http://developer.apple.com/documentation/Cocoa/Conceptual/CoreData/Articles/cdFAQ.html#//apple_ref/doc/uid/TP40001802-243775

----

**Perceived Response**

I spend a lot of time making sure that my 'perceived response' is OK for the user. With CoreData we have the options of using SQLite, XML, Binary or inMemory types for the persistentStoreType (though I'm not sure that inMemory is really a "persistent store".

I have done some performance tests, using the same NSManagedObjectModel (Mom) as follows:

1. Load the ManagedObjectContext (Moc) with 31,344 instances of objects and 33,030 instances of relationships.

2. Save the ManagedObjectContext (Moc) to its persistent store.

3. Release the Moc.

4. Re-open the Moc

5. Print a batch report of all objects and attributes in my model (same code for all options).

Here are the results (all values are in seconds)

    
---------------------------------------------------
                          XML    SQLite    Binary
---------------------------------------------------
Open Moc and Load Data     9       9         9     seconds
---------------------------------------------------
Save Moc and Release      13       9        10     seconds
---------------------------------------------------
Reopen Moc                31     0.02        3     seconds
---------------------------------------------------
Generate Report           32     443        28      seconds
---------------------------------------------------
Time to save small       1.6     0.2       2.5      seconds
change for user
 ---------------------------------------------------
File Size                10.7     0.8      9.8     megaBytes
 ---------------------------------------------------



So the SQLite option needs some way of batch unfaulting in the Moc? I've tried various options as per the published info on faults but haven't found the trick (if there is one).

----

Describe your model:  generating the report may be slow with SQL store if you are using not prefetched relationships... -- Aur�lien

----

**Audit Trail**

I have built an application using CoreData with an SQLite store and  I need to save a permanent audit trail of every change that is made to the database in chronological order.  I then need to build a report that lists who did what to the data and when. 

This is a bit like a permanent undo stack saved to a disk file. The application is NSDocument based and the user can undo and redo at any time whilst he is using the application.

Each of the 12 EntityTypes in my Managed Object Model is implemented by a separate subclass of NSManagedObject.

I could add glue code to override every setValue:forKey and append an audit trail file with the previous for each attribute,  but that does not seem to trap the user invoked undos and redos from the NSUndoManager.

Maybe I could intercept the stuff as it is added to the undo and redo stacks?

Once I have my audit trail saved to a file it should be relatively easy to roll it back.

How do I create my audit trail?

I have already subclassed NSManagedObjectContext and it has a method called saveMoc that does a few things and then calls the moc save: method.  Maybe,  I should query insertedObjects, updatedObjects and deletedObjects within my saveMoc and at this point write the audit trail?

Because every entityType is implemented by its own subclass of NSManagedObject then maybe I should override each of the willSave methods and write my audit trail from there?

The first option looks better from a code maintenance point of view - I only have to do it once and forget it even if I change the Managed Object Model.

In my saveMoc method I now have (where self is the NSManagedObjectContext)

    
   [self logChanges:[self insertedObjects]];
   [self logChanges:[self deletedObjects]];
   [self logChanges:[self updatedObjects]];


and the logChanges method is something like:

    
-(void)logChanges:(NSSet*)changes
{
  id ID;
  long kk = 0;
  NSEnumerator *e = [changes objectEnumerator];
  while(ID = [e nextObject])
    {  NSDictionary *dict = [ID changedValues];
       NSArray *keys = [dict allKeys];
       for(kk = 0; kk < [keys count]; kk++)
          {  NSString *key = [keys objectAtIndex:kk];
             NSLog(@"URI = %@, Key = %@, NewValue = %@, OldValue = %@", 
                               [ID objectID] URIRepresentation], 
                               key,
                               [dict valueForKey:key], 
                               ID committedValuesForKeys:[[[NSArray arrayWithObject:key]] valueForKey:key]);
          }
     }
   //add a timestamp to each record ?
   //add a group to the batch of records ?
   //write them all to the audit trail ?
}


Of course the NSLog gets changed to a file-write in the final version.

The volume of info for a deleted object is still a problem to unravel because some objects have lots of relationships.

I've epanded the logChanges method to extract just the info I need for my audit trail but because it is populated from the moc into 3 NSSets, the records are not in  correct chronological order.

So I guess that I have to subclass NSManagedObject (let's call it MyManagedObjectClass), and make every EntityType implementation a subclass of MyMananagedObjectClass instead of being a subclass of NSManagedObject.  Then instead of calling logChanges from my saveMoc method in my moc, I would need to invoke logChanges from a willSave method within MyManagedObjectClass.

Will that get my audit trail records in chronological order?  Is there an easier way of creating my persistent audit trail?

Now have all my EntityTypes implements as subclasses of MyManagedObject and the willSave calls the logChanges method.  The inserted records still have temporaryID so I guess that for inserted records I have to do something in didSave.

But I cannot get the audit trail to be in chronological order so after a batch load I cannot simply roll back the audit trail transaction by transaction.  I have to take the whole "group saved records", set up dictionaries for IDs and do a second pass for each group to resolve the IDs.

----
 
Have you considered using Core Data's undo mechanisms to your advantage? The Undo Manager automatically stores everything you do in chronological order anyway. Why duplicate this?

----

I already use the automatic (for free) undo and redo features in CoreData whilst the user has the application active, but I also want a permanent audit trail after the user has closed down the application that contains all the permanent changes that he/she made during the session. There may be a number of users updating the database in different sessions but not concurrently. Modelling applications that are multiuser concurrently are problematic so I adhere to the KISS principle wherever possible.  Each record in the audit trail needs to be timestamped and also record the user's name.

----

If you want to have a little 'Change log', add it to the top so we know what you changed when the page pops up in the RecentChanges.

Anyway, I am also not sure what he meant with the Undo manager. Maybe that you could use the Undo manager to record your trail, e.g. by subclassing it or adding a category to catch the calls to it. Subclassing it might work if you overwrite the     -forwardInvocation method that is used by NSUndoManager to record the changes. Then use an instance of that subclass as the undo manager of the context. Sorry just throwing a quick idea here, it needs to be refined and may just not work at all, not sure. In any case, what you are trying to accomplish is potentially useful for others.  --CharlesParnot

---- 

I'll try timestamping the records as they get added to the Undo stack, avoiding the obvious infinite loop.  Then, during the managedObjectContext's save: method I create my Audit Trail. That way all my Audit Trail records are in groups and within each group I know the correct chronological sequence.  I can then do the appropriate sorts when using the Audit Trail.

My CoreData Audit Trail procedure now looks like:

1. NSManagedObjectContext is subclassed and and contains a method saveMoc that:

   (a) gets a timeStamp

   (b) creates an empty array for the changes to the database

   (c) calls [moc save:error] (every changed object will now get a willSave and a didSave message).

   (d) (e) (f) see later at step 5

2. Each Entity in the model (except AuditTrailRecord) is implemented by a subclass of MyManagedObject which is a subclass of NSManagedObject

3. MyManagedObject::willSave - firrst calls [super willSave[ and then records the pre-save values using isInserted, isDeleted, isUpdated, changedValues, committedValuesForKey. The added and deleted  Relationships need to be got using the NSet minusSet: method.  At this stage I only need to record four values for each auditTrail record - the object's id, the key, the action (insert, update, delete) and the previous value for the key or the relationship id.

4. In MyManagedObject:didSave I replace any temporary objectID's with the persistent objectID's. The temporary ID's may be for either the objectId itself or a related object's ID. I also flag the audit trail record as 'saved'.

5. Back in the NSManagedObjectContext's saveMoc

  (d) check for errors returned from save:error 

  (e)  write the records in the Audit Trail array to my database table called AuditTrailRecords, timestamp them and sequence them (in saved order). 

  (f) release the array and all the objects I'm hanging on to for the audit trail

I haven't tried rolling it back - that's another lot of problems to solve because any previously deleted objects that are re-inserted in the database will then get a new objectID and that will cause problems for identifying records further back in the audit trail. What a tangled web I weave.

FWIW: This has got to be the most difficult audit trail I have ever written. And BTW my audit trail has highlighted a set of ghost objects in my database - i created a particular object in two places and forgot what the left hand had been doing so the unused object got put away and was never used or referred to.

