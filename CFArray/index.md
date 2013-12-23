---
layout: page
title: CFArray
---

It's like an NSArray, but crunchier. You have to type a lot more to use one.

 http://developer.apple.com/documentation/CoreFoundation/Reference/CFArrayRef/

It can hold arbitary data, including raw numbers and structs, and includes a binary search function. In other ways, NSArray and NSMutableArray are comparable or better.

Actually, NSArray and NSMutableArray are not better or worse; they're all the same. TollFreeBridging means that any CFArray is an NSArray and vice versa, even the ones you make yourself via subclassing.

Simple examples of calling CFArray functions can be found at
http://www.carbondev.com/site/?page=CFArray

Notably, calling CFA<nowiki/>rrayCreate with NULL for the callbacks parameter creates an NSArray that uses pointer equality and doesn't retain its members.

