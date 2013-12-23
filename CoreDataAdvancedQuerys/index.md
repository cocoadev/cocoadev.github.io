---
layout: page
title: CoreDataAdvancedQuerys
---

I love CoreData, its so easy for simple lookups. But I'm trying to do some more advanced things now and running into trouble.

Namely I'd like something like MYSQL GROUP BY function, if I have a managed object of structured like this:

timeData
{
int year;
int month;
int day;
fileData* images;
}


I'd like to quickly get the years with images.count > 20 without getting the whole array and searching for each unique year. Can I do this? I have looked through the predicate guide and I don't see any mention of this.

tia

----

You'll need a bit of NSPredicate and a bit of key-value magic.

    
NSArray *times = [array filteredArrayUsingPredicate:[NSPredicate predicateWithFormat:@"images.count > 20"]];
NSArray *uniqueYears = [times valueForKeyPath:@"@distinctUnionOfObjects.year"];
NSLog(@"%@", uniqueYears);


I'm assuming here that the images are part of a relationship and not some object.  If it's an object, you'll have to implement a count method.  If it's a one-to-many relationship, the above should work.  I think.  Hope that helps.  You could combine all of that into one line but I kept each step separate for readability.

More information on array operators: http://developer.apple.com/documentation/Cocoa/Conceptual/KeyValueCoding/Concepts/ArrayOperators.html

-G

