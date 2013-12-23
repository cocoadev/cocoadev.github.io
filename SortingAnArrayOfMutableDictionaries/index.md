---
layout: page
title: SortingAnArrayOfMutableDictionaries
---

I currently have an NSMutableArray of NSMutableDictionary objects.  I want to sort the array based on one of the fields from the mutable dictionaries.  For example the dicationary has 2 keys "filename" and "filepath" I would like to be able to sort the array based on the values of those keys for the dictionary.  Any help is much appreciated.  Thanks

----

Look up the     NSSortDescriptor class. It's similar to     Comparator in Java (and the ComparatorDesignPattern). Or you can use the     NSArray method     sortedArrayUsingFunction:context:.  --TheoHultberg/Iconara

----

Like Theo said, but here's some code:
    int someSort(id obj1, id obj2, void *context)
{
	return ([[NSDictionary *)obj1 objectForKey:(NSString *)context] compare:[(NSDictionary *)obj2 objectForKey:(NSString *)context]];
}
And you could call it like this:
    [array sortUsingFunction:someSort context:@"title"]; // @"title" is a key in your dict

----

Much thanks.  Worked perfectly.

