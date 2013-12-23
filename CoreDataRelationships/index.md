---
layout: page
title: CoreDataRelationships
---



I am probably thinking about this wrong as I am new to Core Data. 

Here is a generic example:
 - I have a Day entity that has a relationship of lunch and dinner  (each has a destination of Menu)
 - Menu entity has a relationship of daysUsed  (ie, if Day has a Menu for lunch, then daysUsed would be the inverse)

What I would like to do is set the inverse of lunch to daysUsed, and also set the inverse of dinner to daysUsed.  But the data modeling tool will not let me do this.

So do I have to handle these manually or is there a better way to do this?

Thanks in advance,

AlanSmith

----

You can't really do this the way you've described in Core Data.  What you could do, however, is have a Day entity that has a to-many relationship     meals to a Meal entity.  The Meal entity could have a to-many relationship     days to the Day entity.  And, finally, you can have Breakfast, Lunch, and Dinner entities that are subentities of Meal that simply inherit their relationships.  These entities can also have transient relationships such as     lunchDays,     dinnerDays, etc. that either don't have inverses or that have transient inverses; the transient relationships can be populated via code.

----

Thanks.. Please excuse the ignorant question, but is a subentity the same as a child class?

----

Another approach: Give Menu two relationships: daysUsedForLunch and daysUsedForDinner.  Both are to-many with a destination of Day.  Set the inverse of Day.lunch to Menu.daysUsedForLunch and the inverse of Day.dinner to Menu.daysUsedForDinner.

----

A **subentity** is a "child entity" in a Core Data NSManagedObjectModel.  It is not necessarily a child class; Core Data does not require that your class hierarchy and your entity hierarchy have a one-to-one mapping.

