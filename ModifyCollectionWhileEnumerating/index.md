---
layout: page
title: ModifyCollectionWhileEnumerating
---

  In Apple's Developer Documentation for NSEnumerator, there is a statement that "It is not safe to modify a mutable collection while enumerating through it. Some enumerators may currently allow enumeration of a collection that is modified, but this behavior is not guaranteed to be supported in the future."
  That statement is fairly clear, but I am not certain if it is guaranteed to be okay to modify subordinate items while enumerating the mutable collection.
  As an example, let's say I have a class called MyPix, and it has instance variables of:
    NSString *aPictureCaption;
    NSImage *aPicture;
    NSRect wherePictureIs;
  Also, I have an NSMutableArray *bigArray of MyPix objects.  Can I modify the instances of aPicture, aPictureCaption, and wherePictureIs, when I am enumerating bigArray (not actually modifying the pointers to the instances of MyPix, just using setter methods to modify the contents of the instance variables)?
  Let's say I have 1000 objects in bigArray, and I am able, at one point in my program, to set aPicture for each of those objects.  So, I do that at initialization of bigArray.  But, I am not able to set aPictureCaption or wherePictureIs for each of those objects until later in program execution.  Apple's recommendation, that I pull aside those objects I need to modify (say into a separate NSMutableArray) and then modify them after the enumeration is finished, is not very palatable (since I would be pulling all 1000 over -- at that point, why bother to enumerate bigArray at all?).

  Of course, the real question is not whether this works today (Apple mentions that it MIGHT), but whether this would violate Apple's statement I mentioned above.  I have not coded this up yet, since if it does indeed violate the statement, there is no point in doing so (with an enumerator, anyway).

----
Yes, this is 100% ok. Modifying the objects in a collection does not constitute modifying the collection itself.

----

To elaborate on the above answer, this is only important if you're adding or removing objects to/from a collection while enumerating. Consider an NSMutableArray with three objects. You enumerate it. When you get to the second object, you remove it from the array. The third object is no longer at index 2, but was moved to index 1 (the second place in the array). The enumerator is expecting a third object in the array. *Oops.*

