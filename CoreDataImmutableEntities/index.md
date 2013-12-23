---
layout: page
title: CoreDataImmutableEntities
---



I have a NSDocument based CoreData application.  There are a set of system wide default entities that should be added to every new document and that users should be unable to delete.  What is the best way to to do this?


----
I think you mean "NSPersistentDocument - based". In any case, pick your favorite NS*Document init method and manually create / insert instances into your context. See the Core Data Programming Guide for the details.

----
OK think I haven't explained well enough.  I appreciate that you can add defaults entities in an NS*Document init method but that wasn't my main problem - may be easier with a concrete example:

In my NSPersistentDocument -based app (my bad) I have a core data entity called Location.  Every new document comes with 2 default Locations (Home, Office).  Users can add and delete their own Locations from the UI.  Unfortunately they can also delete the 2 default Locations from the UI.  I want to make it so they can't delete the 2 default Locations.  What's the best way to stop them?  Ideally this needs to be an easily replicable solution as I have lots of entities that I want to be able to exhibit this behaviour.

----
You'll have to code that yourself; CoreData has no concept of read-only or immutable instances of entities.  One way to code this would be to implement an NSArrayController subclass that checks to see if the controlled objects have an "immutable" property and what its value is before responding YES to -canRemove, handling a -remove: action, or handling a -removeObject: message.

