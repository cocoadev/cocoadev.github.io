---
layout: page
title: CoreDataSavingIndividualChanges
---

I have a basic but working app with a few entities and windows for editing them (made by option-dragging to IB). I know it is possible to save / rollback all changes which are in memory but not yet committed to disk.

Is it possible to just save/rollback certain changes?

Example:

Window A has unsaved changes to entity A

Window B has unsaved changes to entity B


Is there any way to just commit the changes to entity A to disk and leave the ones relating to entity B in memory?

Any help appreciated,

BenBarnett

----

The only way I know how to do this is to use a separate managed object context for each window. You can save each context separately.

You are out of luck if you want to save some, but not all, changes in one MOC.

----

OK, thanks for helping there. I found someone else who was trying to achieve a similar thing (link below):

http://lists.apple.com/archives/cocoa-dev/2005/Apr/msg02231.html


I've got it working with a single NSPersistentStoreCoordinator in the app delegate, then multiple NSManagedObjectContext objects (one per window).

It's worth noting that this doesn't work properly with NSXMLStoreType. Committing changes from one an then another managed object context ends up with the first overwriting the second. It works OK with NSSQLiteStoreType though.

