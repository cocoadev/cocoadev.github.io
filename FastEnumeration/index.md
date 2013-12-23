---
layout: page
title: FastEnumeration
---

While going through New features of Objective C-2.0, I came across "Fast Enumeration" in objective C-2.0 which we will be using in Leopard. How can I implement a custom class that supports fast enumeration, I tried by implementing the NSFastEnumeration protocol. The protocol comprised a single method�countByEnumeratingWithState:objects:count:. Still I could not able to use. 
Please feel free to enlighten me.

----

Many infos can be found in the Apple branch of gcc repository, which is not under NDA because it's open source.

----

https://developer.apple.com/leopard/devcenter/docs/documentation/Cocoa/Conceptual/ObjectiveC/Articles/chapter_7_section_1.html#//apple_ref/doc/uid/TP30001163-CH18

----

How can you get the index of the current loop during fast enumeration?

----

Same as with any iterator - set a local variable to count the number of iterations:

    

int index = 0;

for (NSString *element in array)
{
    NSLog(@"element: %@, index = %d", element, index++);
}



As with any enumeration, the index might not really represent the true index of the object, because the enumeration depends on how the collection itself implements it. A "safe" way would be to ask the collection for      - indexOfObject: but doing that will be dog slow as you are then imposing a linear search for every iteration of the enumeration. This is not what enumerations are for! If you need to count through the index, you may as well use the tried and true     for(i=0; i < max; ++i ) approach since that's what it's good for. Enumerations are designed for something else. However, it's true that sometimes you nevertheless need to count iterations of an enumeration and the above code will do that. --GC

----
For built-in Cocoa classes the enumeration count will either equal the index of the object, or it will be meaningless. For example, NSArray and NSPointerArray will both be guaranteed to enumerate in the same order as the indexes of the contents, so the numbers will be equal. For other collections classes such as NSDictionary, there are no indexes at all. Of course nothing says a custom class has to keep everything in the same order, but Cocoa should be doing it.

----

Right - though I was trying to make as general a statement as possible. Since you can also use fast enumeration with NSEnumerator which could be a reverse iterator, the index count would be mostly wrong unless you also ensure it counts backwards, etc. As with all handy or cool features, they don't write your code for you - you still have to have a clue ;-)

