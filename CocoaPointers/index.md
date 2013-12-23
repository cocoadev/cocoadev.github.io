---
layout: page
title: CocoaPointers
---



Hello,

I am putting several objects into an array. I want to put pointers to those objects in a different array. I am guessing that this has to do something with shared_ptr, but I can't understand the documentation. Can someone help me?

Thanks

----

shared_ptr is a C++ thing. Are you using ObjC++? or C arrays? or NSArrays?
----
I am using NSArray.
----
It isn't clear what you're trying to do yet.  In objective-c, we always refer to our objects through pointers.  If you declare     NSNumber *aNumber or     id aNumber, then aNumber is a pointer.  It's rare in Cocoa to need to take a pointer to the object pointer.

Right now, I'd guess that you just want to add your object (pointers) to two different arrays.  
    
id anObject, anotherObject; // assume these exist
NSMutableArray *anArray = [NSMutableArray array];
NSMutableArray *anotherArray = [NSMutableArray array];

[anArray addObject:anObject];
[anArray addObject:anotherObject];

[anotherArray addObject:anotherObject];


Now the 0th element of anArray is the same object as the 1st element of anotherArray.

