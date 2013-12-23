---
layout: page
title: CoreDataMostWanted
---

I'll start the ball rolling with a few wishes for CoreData. I'll number them CDMWnn -  U2 please. 
Keith

----
CDMW1: Friendlier error messages (Yes:3 No:0)

Rather than stuff like "An error occurred whilst saving" or "An uncaught exception was raised".

For Example: Compile and run the example for CoreData/TilePuzzle. Then change TILE_GRID_SIZE from 3 to 4, recompile and run again without first deleting /user/library/application support/timepuzzle.xml.
----
CDMW2: Easier Version Control and Maintenance for upgrades to the Managed Object Model. (Yes:5 No:0)
Yes, at WWDC 2005 the Core Data Team recognize that migrating to a new model is a "very difficult task", and even recognize that they do not have any solution for the moment to ease it...

----
This is greatly simplified in 10.5 where you can perform almost automatic migration. I'd say that Apple definetly has listened.

----
CDMW3: Fast batch unfaulting of SQLite data. (Yes:1 No.0)

Maybe simply a fast migration to an InMemoryStore?


----
See "Batch Faulting and Pre-fetching with the SQLite Store", a subsection of http://devworld.apple.com/documentation/Cocoa/Conceptual/CoreData/Articles/cdPerformance.html.
)

----
CDMW4: An Audit Trail that records all permanent changes made in a user session.  (Yes:2 No:0)

So changes can be rolled back in a subsequent session. Also useful to report changes made to the database during a session or over a specified period of time by a particular user.
----
CDMW5: Support for ordered to-many relationships. (Yes:3 No:0)

I voted yes, but I think it might already be in there - take a look at http://developer.apple.com/documentation/Cocoa/Conceptual/ModelObjects/Articles/moAccessorMethods.html#//apple_ref/doc/uid/TP40002132 - of course, this is only if you maintain the ordering yourself; it would be much nicer if CoreData did it for you.

----
CDMW6: Performance improvments when fetching SQLite data. (Yes:1 No.0)

Official answer from apple is 25,000 objects / seconds on a 2x2Ghz G5 with tons of ram. On my powerbook 1.5Ghz it is about 10,000 objects / seconds which is too slow. Many fetch requests takes more than 5 seconds to execute and return the fetched data.

----
CDMW7: Autoincrementing Attributes. ( Yes:2 No.0)
its a pain writing a value that goes up 1 for every entity created.
----
Maybe write an NSManagedObject subclass that maintains a key that's autoincremented when an entity is created?  Shouldn't be *too* hard to do.

----
CDMW8: Remote stores

Being able to create a Core Data model for a remote DB and then slap a bindings-enabled interface ontop of it would be great! /Erik Aderstedt

