---
layout: page
title: NSManagedObjectIDPermanency
---

Is a NSManagedObjectID permanently unique ?

For instance, if I delete an object from a NSManagedObjectContext then 
could the same  objectID be reissued by CoreData for another object (of same type) that I insert at a later date for the same persistent store ? 

Before the next [ moc save: ] then obviously the ID cannot be reused. 

But what about 

(a) After the next [ moc save: ] ?

(b) During a subsequent run of the application ?

Keith,  25 Nov 05

----

Have you read the documentation?

http://developer.apple.com/documentation/Cocoa/Reference/CoreData_ObjC/Classes/NSManagedObject_index.html?http://developer.apple.com/documentation/Cocoa/Reference/CoreData_ObjC/Classes/NSManagedObject.html

See the objectID method - the answer is brief and pointed. The API reference should always be your first stop when looking for answers. To help you with this, consider using AppKiDo. The next step would be to follow the links from each object's "Programming Topic" section (if it exists) and read the topic pages very thoroughly.

----
Yes, I have read the documentation. Many thanks for you prompt reply  but the documentation does not seem to explicitly answer my question. Briefly my question was:

If I delete an object from my object graph and from its persistent store then can a subsequently created object get that same objectID allocated to it? 

In the SQLIte schema there is a field Z_MAX_INTEGER in the table Z_PRIMARYKEY, and in the XML file within <database info> there is <nextObjectID>1234</nextObjectID>.

I assume that these are incremented sequentially and the same number is never reused but I cannot find anywhere in the docs that specifically says that this is so.  I don't like to assume anything.
Regards, Keith
----

If it's guaranteed to be unique in a context, then it's safe to assume ids are not going to be reused. This is implied in any such situation.
----
OK so now I know (or can very much assume) an objectID is unique for all time here is the real question.

In my audit trail I have objectrs and relationships that are all identified by URIRepresentations.  If I reinstate a previously deleted object and it gets a brand new objectID then 

    (a)  until I save I only have a temporaryID to play with and 

    (b) some the relationships in the audit trail may now pointing to something that has just acquired a brand new objectID.    

Does anyone know if I can take a 'previously deleted' record and stuff it back into the database reusing the original objectID? 
 
----

Not quite. Until the changes to the context are saved, deleted objects are only flagged (their objectID stays the same) for deletion. If they were part of a relationship (depending on your deletion rules in your MOM), they are removed from the relationship or left alone (by default, they're removed from the relationship. Using CoreData's magic and automatic undo, you could undo the deletion (and relationship removal) all at once.

You might want to search the cocoa-dev list. This topic has been discussed in several threads there.

