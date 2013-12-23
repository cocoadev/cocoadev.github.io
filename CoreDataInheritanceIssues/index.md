---
layout: page
title: CoreDataInheritanceIssues
---

I just thought we should start dividing up the massive CoreDataQuestions page.  This start is with an issue I've had to deal with on multiple occasions and which has greatly confused me, and that is the way in which Core Data deals with inherited relationships.  Short version: create an entity "Animal" and subentities of dogs and cats, etc. Then create a bunch of instances of dogs and cats. If you do a fetch request for animals, YOU WILL GET NONE.  For me, it seems you should get all instances of dogs and cats.

Let's say I have a base class which we'll call MyHierarchicalEntity and which points to itself for its relationships "children" and "parent".  If you create a set of these and add them to your top-level object via, say, [topLevelObject addObject:object1];, etc., normally those dual-relationships are both automatically maintained.  However, if you create a sub-entity to the first and use it in the same way, the parent entity is never set and thus when you quit all relationships are destroyed.  the trouble here more than anything else is two-fold:

1) Data is automatically deleted by Core Data at save, no complaints
2) The runtime and compiler do not complain, giving no warning that your data is going to be lost.

The basis of this problem is that a sub-entity is not considered a kind of its super-entity.  If I create an entity "Person" with relationship "mate", which is defined as a relationship between "Person" and "Person", then create a subentity of person "Woman", if I ever make a "mate" connection from person to woman or vice-versa that relationship is broken down.  Why?  Because Core data thinks that the inverse-relationship of "mate" is a person.  And since "woman" is not a person, it breaks!  This is completely bogus!  

Fortunately there is a short-enough solution to this, and that is that everytime you create an entity you must set both of its relationships, ie 
    
	NSManagedObject *topItem = [NSEntityDescription insertNewObjectForEntityForName:@"Item" inManagedObjectContext:[[NSApp delegate] managedObjectContext]];
	NSManagedObject *newItem = [NSEntityDescription insertNewObjectForEntityForName:@"ItemSubentity" inManagedObjectContext:[[NSApp delegate] managedObjectContext]];
	[newItem setValue:topItem forKey:@"parent"];
	[[TopItem mutableSetValueForKey:@"children"] addObject:newItem];

