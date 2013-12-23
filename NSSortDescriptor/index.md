---
layout: page
title: NSSortDescriptor
---

http://developer.apple.com/documentation/Cocoa/Reference/Foundation/ObjC_classic/Classes/NSSortDescriptor.html

NSSortDescriptor is Apple's rather confusing class, used for sorting arrays, tables, and pretty much anything else. It works based on three pieces of input: a sort key, ascending or descending, and a comparator selector. The sort descriptor first will use KVC to find the value of the key on each element, then call the comparator (    compare:) on each value to figure out where it will end up in the final (sorted) array, table, etc. It is probably at least *slightly* more efficient than this description, i.e., not calling     compare: for *every single object.*

Please help fix up this page, because right now this is not very helpful.

----
How can I sort my table column by filename? Right now, if I sort the values (NSString<nowiki/>s) normally, the objects in different folders are placed differently. Example:
    
JavaClassA.java
JavaClassB.java
ObjCClass1.h
ObjCClass1.m
JavaClassC.java
ObjCClass2.h
ObjCClass2.m

This is definitely not the order I want. The reason for this weird sort? I used an NSFormatter subclass to cut off the path, except for when editing the values. But the table sorts by the full path, not the truncated one (    lastPathComponent, actually). How can I tell the table column (preferably in IB) to sort by the filename only, and ignore the full path? Thanks, JediKnil

