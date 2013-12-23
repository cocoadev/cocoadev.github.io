---
layout: page
title: FoundationCollections
---

The Foundation Collections classes are used for storing arbitrary numbers of objects in one place. There are several kinds of collection: an array, a dictionary, a set, and (ObjC only) a counted set. http://goo.gl/OeSCu

Apple's documentation is at https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/Collections/Collections.html

----**Basics**----

NSArray stores objects in a list or array; imagine a bookcase, with books placed along its length. Objects are retrieved from an array by their index, an integer specifying how far from the first object an object is. Thus indices range from 0 (inclusive) to the size of the array (not inclusive). (See also: DesignArray)

NSDictionary generalizes the principle of index by attaching a unique key to each object. Imagine a dictionary, where information is found by looking for a keyword or phrase. Objects, then, are retrieved from a dictionary by their key.

Any type of object may be used as a key, but note that an efficient hashing function is a must for fast access times. To visualise what a hash is, imagine a small index that tells you the page number of any entry in the dictionary. A hashing function returns this "page number". If lots of keys are stored on the same page, it will be very inefficient to find a given one; if each page has at most one key, access times will be much faster. NSNumber and NSString objects have excellent hashes by default, so are good key types. (See also: DesignDictionary, HashingAndEquality)

NSSet does away with indexes and keys altogether, just storing objects. Imagine a big messy heap on the floor, with no easy way of referring to anything in the mess except by actually holding it up. Objects in a set can only be retrieved by an enumerator (see below), and generally one just checks to see if any object is in a set or not.

NSCountedSet acts almost exactly like a set, except any given object can be in the set multiple times, and the number of times it is in the set can be retrieved. Imagine perhaps a big messy pile of photocopies.

All of these have **mutable** versions - that is, ones which can have objects added and removed, usually in a very similar way to how they are accessed. These classes are NSMutableArray, NSMutableDictionary, NSMutableSet, and NSMutableCountedSet (ObjC only). Each has all the same methods as their non-mutable counterparts, as well as a range of modifier methods.

----**Memory management**----

All objects are sent a retain message when they are added to a collection (eg upon construction of the collection), and a release message when they are removed from it (eg when the collection is released). See also MemoryManagement.

----**More details**----

All collections are examples of ClassClusters - that is, while you only ever deal with the classes I have named above, the object you are actually handling will probably be a derived class (eg NSCFArray instead of NSArray). In class clusters, inheritance serves a different purpose to normal; instead of extending the functionality in the superclass, one has to reimplement it. However, only a few methods need to be overridden (see the relevant documentation), as all other methods are defined to call them.

If, for example, you wanted to access a dictionary-type mutable database, you could subclass NSMutableDictionary and implement count, objectForKey:, keyEnumerator, setObject:forKey:, and removeObjectForKey: to access and alter the database appropriately. Then the dictionary will behave exactly as you would expect. (Of course, for efficiency's sake you could override some other methods - say, allKeys, if the database permitted a rapid way of implementing this operation.)

The mutable collections are all subclasses of their non-mutable doubles. Thus, if you have an NSArray object, say, you cannot be sure it is immutable. If you want an immutable copy, the copy method guarantees to return an immutable version of the array; conversely, the mutableCopy method will return a mutable copy in all cases. See https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/MemoryMgmt/Articles/MemoryMgmt.html for more details of the NSCopying and NSMutableCopying protocols.

----**Other access methods**----

There are several ways of accessing the objects in a collection aside from the main way (index, key, or knowing the object already). For all collections, objectEnumerator returns an object that allows you to access every object in the collection, one at a time. The following works for any collection:

    
 NSEnumerator *e = [collection objectEnumerator];
 id collectionMemberObject;
 
 while ( (collectionMemberObject = [e nextObject]) ) {
     // do something with collectionMemberObject
 }


For a dictionary, a similar method, keyEnumerator, allows you to iterate through each and every key in the collection, in an unspecified order.

Other methods also exist, any of which may prove useful in some situation. See Apple's documentation (link above) for a definitive list of them all.

See also IteratingThroughAnArray for a discussion of the benefits of objectEnumerator.

----**Storing null objects, and other miscellany**----

If you wish to store a "null" object, a placeholder to say "empty space here", NSNull does the job. Any instance of it always equals any other (in actual fact, there only ever is one instance), so can easily be checked for. This remedies the fact that Foundation collections cannot be passed nil as an object (or a key).

If you make a copy of an immutable collection, you will probably get back the original (retained, of course). This should not be noticeable if you balance your RetainingAndReleasing correctly, but if you don't it may make tracing memory leaks and similar problems harder. Making a mutable copy, or any copy of a mutable collection, will always return a new object.

----**Multi-dimensional arrays**----

While arrays are very efficient for the single-dimension they were designed for, it is inefficient to use an array of arrays, ungainly to hash multiple dimensions into a single unsigned explicitly, and wasteful to use a dictionary. For multi-dimensional arrays, check out DesignMatrix.

----**Foundation Collections and Interface Builder**----

By default, Apple's InterfaceBuilder does not handle collections - you cannot modify them nor in many cases even instantiate them. See FoundationCollectionsPalette for a palette to remedy this.

----

IMO Interface Builder is for building UIs. It's a strictly AppKit thing. You shouldn't be doing anything with foundation classes in IB.
----
Quick Q:
Since Obj-C, like C++, is a superset of C (and basically IS C except the compiler turns messages into msg_Send()), wouldn't a C style array of Obj-C objects be feasible?

I mean like:

    
NSString arrayOfStrings[]


----

You can have an array of *pointers* to objects easily enough:     id *objArray; or     NSString **stringArray;.

It's possible to cram a bunch of actual objects into an array, but there are lots of potential problems. Handling deallocation is a large problem. Even creation is a problem, because all     -init... methods are allowed to destroy     self and return a new object. However, it's possible to fix both of these in one shot if you use a custom class whose creation and destruction methods are coded appropriately. Then you could do something like this:
    
 MyObject *objArray = malloc(count * sizeof(*objArray));
 for(i = 0; i < count; i++)
     [objArray + i init];
 [objArray + 2 doSomethingWithThirdObject];

Note that since you use pointers in message sends, you don't want to actually use the [] array notation.

This is likely to be very impractical. However, creating a big array like this would be much more efficient than creating a lot of small objects, and an approach like this could be useful in a situation where lots of small objects are used and the overhead of the allocations is high.

In general, ObjC objects are exactly like C structs, except you are aren't allowed to have a variable directly contain an object, only pointers to objects. So you can do all the same stuff to objects as you can to structs, although that doesn't make it a good idea.

----

why wouldn't you use the subscript operator? there's nothing wrong with:

    
 for(unsigned i = 0U; i < count; ++i)
 	objArray[i] = [objArray[i] init];
 [objArray[2] doSomethingWithThirdObject];


the subscript operator (    *pointer*[*index*]) is different from the message operator (    [*pointer* *selector*]).

I did add the assigment, however. as was mentioned,     -init can return a different object entirely, or no object (    nil). it is bad to blindly assume that     -init did not return anything other than the receiver.

*--boredzo*

----

You're writing code that works with an array of pointers to objects. The above code works with an array of objects. That's why it doesn't use the subscript operator.

----

oh, right.

one other suggestion, though: use calloc instead of malloc.

    MyObject *objArray = calloc(count, sizeof(MyObject));

then the memory will be zeroed for you, much like     +[NSObject allocWithZone:] does.

*--boredzo*

