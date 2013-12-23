---
layout: page
title: CoreDataLibraryGroup
---



Hi all,

well there is one thing that I really would like some advice on, is how to implement a simple 'Library Group' with Core Data. You know, like you have in iTunes, a selection of groups which you can add things to yourself, and a top 'Library' group that has everything in it.

Now, getting the groups working is simple - I have a     Group entity and a     Thing entity. Things go in Groups (a Group has a 'to-many' cardinality relationship to Things).

Of course, I have this working through Cocoa Bindings to display my list of groups on the left and my list of things on the right, just like the iTunes/iPhoto interface.

But one thing I have found non-trivial is setting up a Library group that shows /All/ of the Things. At the moment, I have a sub-entity of     Group, which is called `LibraryGroup`. LibraryGroup has it's own custom class, and I ensure that there is one and only one     LibraryGroup in the MOC at awakeFromNib time. I have overridden the `- (NSSet*)` things method in LibraryGroup to fetch all Objects of type     Thing from the managedObjectContext, put them in a set, and return them. This works, but when the     Things are added or deleted, my tableview doesn't refresh, I have to select a different group, and then select back to the LibraryGroup before changes take place :(

So my question is this:

1. If anyone can assist in the issue above with my tables not updating that would be fantastic... I think I need to observe the MOC for changes, and refresh, but... I am new to CD and not familiar with how I should do this.

2. Groups with a main Library entry is a very popular way of organising data in lots of GUIs, and there must be loads of ways to skin this cat, but I haven't come across a simple and basic implementation of it in CoreData online (if I had, I wouldn't be bothering you fine folks :) ). There's CoreRecipes, but, to be honest, it's a massively overcomplicated example that doesn't teach beginners like me these basics too clearly. So I was wondering if anyone had an basic implementation of a CoreData "Groups + MainLibraryGroup" structure, or could point out whether my attempted route outlined above was worth continuing with?

Many thanks for your time and efforts, they're received with great dollops of gratefulness :)

- Pete

----

How about just using an NSArrayController for the Group entity. If you don't add any predicate, the controller will harvest all of the ojects and will get updated as they are added or deleted. No need for an explicit LibraryGroup with a to-many relationship.

----

________________

I'm looking to do this as well. I'm learning Core Data at the moment and this is something I'm really trying hard to figure out.  Did the method above work, or does anyone else have any other hints?

Cheers.

