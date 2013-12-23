---
layout: page
title: CoreDataBehaviorNotes
---

This page is for documenting some of the behavior notes for the CoreData framework that are not otherwise mentioned in the documentation.

----

- (BOOL)isDeleted
This method returns YES if the object has been deleted and will be committed with the next save.  It continues to return YES even if the object is re-inserted prior to the save.  Only saving the context will reset this value to NO.  This method returns NO if the object has never been removed from a context since the last save.  This means even if it has been removed, once the context is saved, the object no longer considers itself deleted even if still retained.

