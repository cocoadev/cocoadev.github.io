---
layout: page
title: NSPredicateForCount
---



The Core Data app I'm working on essentially has two entities: let's call them Groups and Items. A Group entity has a to-many relationship to the Item entity. How do I create an NSPredicate that returns all Group items that have no Item entities? I think it has something to do with NSExpression, but the function that does counting requires an array of values.

Any ideas?

----

NSSet has a count method.  I suppose you could try that.

    
NSPredicate *p = [NSPredicate predicateWithFormat:@"items.count == 0"];


If that doesn't work, you could always create an NSManagedObject subclass for Groups and have a hasItems method.

    
// In Group : NSManagedObject subclass
-(BOOL)hasItems { return self valueForKey:@"items"] count] > 0; }

// Elsewhere
[[NSPredicate *p = [NSPredicate predicateWithFormat:@"hasItems == 0"];

