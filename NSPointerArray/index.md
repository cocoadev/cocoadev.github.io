---
layout: page
title: NSPointerArray
---

A new Leopard class which allows for a NSMutableArray-like object that holds arbitrary pointers (much like a CFMutableArray can do with custom retain/release callbacks in CoreFoundation). Supports most operations (ie enumeration, mutation, dynamic growing...) but does not inherit from NSArray.

A key difference from NSMutableArray is that it can hold weak pointers under GarbageCollection and nil is an allowed value. Weak pointers to objects which are collected turn into nil automatically.

