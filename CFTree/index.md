---
layout: page
title: CFTree
---

Apple documentation:

*http://developer.apple.com/documentation/CoreFoundation/Conceptual/CFCollections/Concepts/types.html
*http://developer.apple.com/documentation/CoreFoundation/Conceptual/CFCollections/Tasks/usingtrees.html
*http://developer.apple.com/documentation/CoreFoundation/Reference/CFTreeRef/index.html


----

CFTree is a CoreFoundation class to implement tree structures. It is not TollFreeBridged with a Cocoa class. It is unique among CF's collection classes in that an instance of CFTree does not represent the entire tree, but instead is one node of the tree.

Each instance of CFTree has a parent CFTree instance, a number of child CFTree instances, a pointer to the node's data, a retain callback, a release callback, and a description callback. A CFTree with no parent is the root node. The data pointer and callbacks are in a CFTreeContext struct.

You don't have to use the pointer as a pointer. You can put a long integer there if you like. But if you do use it as a pointer, you have five options to deal with the data's lifetime:

*You can use a built-in CoreFoundation type, such as CFNumber or CFData, or a bridged Cocoa equivalent. The retain, release, and description call-backs can be set to CFRetain, CFRelease, and CFCopyDescription respectively.
*You can store an ObjectiveC id, but you'll have to write C callbacks that call the object's **retain**, **release**, and **description** methods.
*You can keep an object pool. Everything that gets added to the tree goes in the pool, and the pool gets freed when you are done with the CFTree. You won't need to implement the retain or release call-backs.
*You add a reference count to the data, or make a wrapper that includes a reference count. You would then have to implement the retain and release callbacks.
*You can track lifetime separately somehow. You would have to implement the retain and release callbacks.


Parents retain children, but children do not retain parents. No cycles are allowed (i.e. a node's ancestor cannot be one of the node's children).

Operations include sorting children and applying a function to children. These operations are not recursive (i.e., they don't affect the children's children).

Simple example code of calling these functions can be found at
http://www.carbondev.com/site/?page=CFTree

----

I thought I remembered there being a multitude of CFTree Cocoa wrappers but when we were discussing it in #macdev today we couldn't find one.  I took the liberty of knocking one out and releasing it under the BSD licence for your programming pleasure.

Goto JKPTree for details.

JKP

