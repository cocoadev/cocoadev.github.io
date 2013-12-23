---
layout: page
title: UniqueIDInCoreData
---

I'm trying to understand how best to implement a uniqueID for records inserted in General/CoreData entities (sql store if it matters).

I can't seem to find any relevant verbiage in the Apple docs or google or here.
But these things are a function of understanding -- you have a hard time searching for things if you don't understand them...

The default values don't appear to offer a way to do this, neither do the attribute settings themselves.

So it seems that I'm on my own to manifest an ID (I have found how to create General/UUIDs) and get it into the appropriate attribute.

Is this correct?  Is there a better way?

Thanks for any tips, pointers, references, etc..

BTW:  where would I have found documentation on being able to set date default values to @now?
I found this by sheer poking and mumbling...

----

By reading the *Core Data Programming Guide* and following the references to the other documentation. Everything you've asked is well-documented.

----

What is the unique ID going to be used for? If it's just to be able to refer to another entity, you don't need one as core data keeps track of them for you. It took me a while to figure this out.

----

General/NSManagedObject has -objectID which is guaranteed to be unique in the current context and stay the same once the object is saved to the store. The id of the object before the save is temporary. The Core Data documentation would be an excellent place to start looking when looking for Core Data information. :-) If you're looking for creating your own unique values, you could look here: General/IDentifiers

----

Thanks for the pointer to General/NSManagedObject! 
However, in my feeble defense, this is actually this is a good example of what is so frustrating about Apple documentation.  I had read through all of:  "Data Modeling Guide"; "Core Data Programming Guide"; "Model Objects Implementation Guide"; "General/NSPersistentDocument Core Data Tutorial"; and the "Low-Level Core Data Tutorial" before posting this question.  The topic of object identity is never mentioned in these docs.  Apples stuff is academic in is tone.  People out in the real world need in line sample code snippets and usage case examples...

A good 3rd party book on General/CoreData and Bindings would be a relief.

----

That is why the API reference exists. http://developer.apple.com/documentation/Cocoa/Reference/CoreData_ObjC/Classes/General/NSManagedObject.html  ... it is up to you to read all of the relevant documentation. This wiki is definitely not a substitute for the reference material but rather a supportive augmentation. For easy navigation of the API reference, see General/AppKiDo.

----

hmm, this is a year later... there is need to do what this guy wants, I think...I'd like to be able to do a predicate search through relationships using a guid or something like that. I can't search by the objectID (at least I can't get it to work...it isn't in the database, right?)

----

The object ID is in fact saved to the persistent store.  (More accurately, the information necessary to recreate an equal object ID is saved to the persistent store.)  You can use an object ID in a predicate interchangeably with the object itself; just use the %@ placeholder syntax in the predicate format string, and pass the object ID as the corresponding argument.  Note that if you're searching to-many relationships you will need to use the appropriate predicate syntax (ANY, ALL, CONTAINS/IN, etc.) because there's no way to access "foreign key columns" or anything like that using predicates.  This is because "foreign keys" are a low-level store-specific concept that Core Data doesn't expose via its higher-level API.
