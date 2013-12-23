---
layout: page
title: DesignDictionary
---

**Object Design:** *Dictionary collection class*

----
**The Dictionary design**----

The dictionary class of collection designs addresses a weakness of DesignArray: namely that objects must be referenced by an unsigned integer. In a dictionary, any object can be used as a key, and the key is stored in the dictionary along with the object it refers to.

Cocoa already has a DesignDictionary class: the NSDictionary class cluster. To allow some degree of efficiency, every object must provide a hash value from -hash which, while not unique to the object, must be the same for any objects that return YES to -isEqual:.

Advantages:
* As flexible as you could want; you can even use class objects as keys
* Acceptably fast, given a good hashing algorithm


Disadvantages:
* The number of potential keys far outnumber the number of values -hash can return, making optimizing very hard
* A bad hashing algorithm will reduce the object retrieval time of a dictionary to o(n)
* Keys must be stored alongside objects, potentially wasting large amounts of memory
* DesignDictionary is an unacceptable replacement for mostly-full arrays both in terms of speed and memory usage


See also: NSDictionary, FoundationCollections, DesignArray, DesignMatrix

----**Comparison with C++'s map<>**----

The main distinction between the dictionary design and the similar construct used in C++ - the map - is that the latter requires a strict ordering on the keys used. This allows for very good efficiency - o(log(n)) at all times, by using a DesignRedBlackTree - at the cost of being less general and more complex in use. Cocoa does not offer an equivalent class type.

