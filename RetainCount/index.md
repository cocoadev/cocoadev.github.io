---
layout: page
title: RetainCount
---



Cocoa's memory management, called reference counting, is rather ingenious. It's approximately halfway between C's malloc/free (and, for that matter, C++'s new/delete), and the automatic GarbageCollection which Java and SmallTalk both use. It is much more elegant than malloc/free, and affords more control than Java and SmallTalk-80's GarbageCollection.

Internally, while your code is going about the normal RetainingAndReleasing, the system is using a RetainCount to determine when an object will receive a dealloc message.

When you send an object a retain message, its RetainCount is incremented. Similarly, when you send an object a release message, its RetainCount is decremented. When the RetainCount is zero, the object is sent a dealloc message.

NSObject (and all of its subclasses!) has a method called retainCount that you can use to get an object's current retain count. Handy occasionally for debugging, but maybe you should check out OmniObjectMeter?

The RetainCount and its messages are defined in NSObjectProtocol, and are thus implemented in all other classes.

You may also be interested in reading the documentation on WeakReferences.

See also < http://www.memorymanagement.org/glossary/r.html#reference.counting >

----

Having rooted about in the Darwin code, and on the (fairly reasonable) assumption that Cocoa retain is implemented very like CF retain, I can tell you the following things about the retain count of an object: 
* If it is < 0x8000 (that's 32768 for those of you who don't read hex), the retain count is stored with the object itself
* If that count exceeds 0x8000, the retain count is instead stored in a special dictionary; hence one can retain as often as one wants (up to a 32-bit signed integer's maximum value), but the cost goes up after 32767 unbalanced retains
* The whole retain/release code stuff is protected by a simple spin lock once multithreading is turned on in an app; hence retain/release becomes more expensive when multithreading is used

It seems very likely that the exact location of the retain count is different between CF and Cocoa (the latter can be sneaky and place it before the object, while CF cannot hide such tricks so keeps it inside the structure). This may be important to people writing bridge classes!

-- Postulated by KritTer

----
Cocoa doesn't do any sneaky things like placing retain counts before the object. This can be verified by, say, allocating an object and then calling     malloc_size on it, which returns a sensible number and shows that     +alloc is returning the pointer straight from     malloc. And since there is no place for such a count in the object, it must be entirely exterior.

----
You might also note this comment from an apple guy:  http://www.cocoabuilder.com/archive/message/cocoa/2001/4/29/23920   about a seemlingly -1 retain count. (UINT_MAX actually).

