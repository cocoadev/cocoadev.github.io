---
layout: page
title: DesignArray
---

**Object Design:** *Array collection class*

----
**The Array design**----

The array is one of the most common collection types in programming, because it is how memory works in most computer designs. The premise is simple: every chunk of information you store is assigned a unique number, called an index, and that index is then used to access the information again later. More specifically, the chunks are ordered (arbitrarily) and assigned each number in turn, from 0 (or 1, in some languages) up to the largest index used.

Because this system is how computers themselves store data, DesignArray can be heavily optimized in both memory usage (lowest possible, give or take a few bits per object) and access times (constant time), although at the expense of being costly to increase in size (roughly speaking, constant time or o(n), depending on your luck).

Cocoa comes with its own implementation of DesignArray: the NSArray class cluster. The exact implementation details of this are unclear (I would expect heavily optimized for the fixed case, weakly optimized for the mutable, anyone with concrete data?).

Advantages:
* Can be heavily optimized
* Very common concept used in almost every programming language
* Implicit ordering of objects can be enforced to some specific scheme to make location of objects very rapid (BinomialSearch)


Disadvantages:
* Not inherently very general
* Terribly memory-inefficient if the array is mostly empty


See also: NSArray, FoundationCollections, DesignDictionary, DesignMatrix, DesignDoublyXORLinkedList

----

Well, we have the source code of NSArray (or rather the subclass NSCFArray) in the form of the CoreFoundation CFArray:

http://www.opendarwin.org/cgi-bin/cvsweb.cgi/src/CoreFoundation/Collections.subproj/

-- PeterMonty

