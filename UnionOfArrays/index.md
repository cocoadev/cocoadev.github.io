---
layout: page
title: UnionOfArrays
---



The Apple documentation is unclear discussing valueForKeyPath:

@unionOfArrays
The @unionOfArrays operator returns an array containing all the object values in the arrays returned by sending valueForKeyPath: to each item in the receiving array, passing the key path to the right of the array operator as the parameter. Unlike @distinctUnionOfArrays, duplicate objects are not removed.

@unionOfObjects
The @unionOfObjects operator returns an array containing all the objects values returned by sending valueForKeyPath: to each item in the receiving array, passing the key path to the right of the array operator as the parameter. Unlike @distinctUnionOfObjects, duplicate objects are not removed.

Can someone give an example of how these differ?

----

Seems clear enough. If you use the @unionOfObjects operator with a key path that returns an array, you'll get an array of arrays. If you use the @unionOfArrays for the same key path, you'll get an array of all the objects from all the returned arrays. Make sense? -CS
