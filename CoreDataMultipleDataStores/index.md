---
layout: page
title: CoreDataMultipleDataStores
---

Hi all, I am facing a problem that I can't figure out. I've been at it for almost three days now. I am using CoreData for my data model, and have several entities set up, blah blah. The main entity (Group) has a relationship to another entity (Event), which in turn has a relationship to another entity (Person). Each entity in succession is the content set for the previous one (i.e. Events are contained in Groups, Persons in Events).

I have set up a second, in-memory data store so I can insert a special "Library" group ala iTunes or iPhoto, which is recreated every time the app starts. Everything works fine except I cannot save objects in the Person entity upon exit. Groups that are added save, Events that are added save, but if I add a Person and then exit, I get the "Can't reassign an object to a different store once it has been saved." message in the log. If I remove the methods that create the in-memory store, it works fine. 

Has anyone else experienced this, and is there a remedy? I have traced it back to the second store, but it doesn't matter which entity it is, as long as it's the third in line, connected to the Event entity. I added another 'third-in-line' entity and it still does it. Any ideas? --LoganCollins

----

You're a little imprecise in your description of what you're doing above.  You're saying entities are content sets�� they're not.  Entities are entities, content sets are a controller-level construct.  It often makes it much easier to figure out problems both on your own and for other people when you're precise in your thought about the problems and your descriptions of them.

That said, it sounds like you're creating a "Library" Group instance associated with your in-memory persistent store and then creating relationships between this Group and objects from other persistent stores.  CoreData does not support cross-store relationships, so if you ask an NSManagedObjectContext to save this object graph it will return the error you describe above since it will be trying to save a relationship between an object in one store to an object in another store.  You'll need to break any relationships between your "Library" Group and objects from other stores before attempting to save your object graph.

One other thing that you might be doing is associating only some new instances of your entities with persistent stores when you create them.  Since you're working with multiple persistent stores, unless you used configurations when adding the stores to your persistent store coordinator, they may wind up assigned to an unexpected store upon save.  The easiest way to resolve this is to use a subentity of Group for your "Library" group (call it LibraryGroup), and give your model two configurations, one that doesn't contain LibraryGroup and one that contains only LibraryGroup.  Then specify these configuration names when you add your real persistent store and your in-memory persistent store, respectively, to your coordinator.  This will let the coordinator know which persistent store should be assigned new instances of which entity automatically, without having to manually assign the objects.

----

----

10 dec

For me the same kind of question, and would like this to figured out. Is there any sample what uses more then one datastore in code or created with drag and drop in xcode -> IB?

Thank You

----

It depends how you're creating the Person objects. If you're doing it using the default stuff in IB, you have to make sure that the right managed object context is bound to the array controller that's add: or insert:ing the object. If you're doing it in code, you can choose the managed object context in     -[NSEntityDescription entityForName:inManagedObjectContext]. --BenStiglitz

----

I do it that way in code and in IB, and it still does it. I use CoreData's default context code that is implemented when you create a new project in Xcode, and in my "newPerson" method I use     [NSEntityDescription insertNewObjectForEntityForName:@"Person" inManagedObjectContext:[self managedObjectContext]], which is also what I use for adding groups and events. --LoganCollins

----
So     self is your document, right? How is the second context created, and how do your refer to it, and how do you add objects to it?

