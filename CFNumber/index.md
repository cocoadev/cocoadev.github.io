---
layout: page
title: CFNumber
---

The CoreFoundation version of NSNumber, for use in vanilla-C code.  Also, NSNumbers usually show up as this in ObjectAlloc, as the two can be freely interchanged with TollFreeBridging.

*Not exactly true...NSNumber is a ClassCluster, so they are not really identical...CFNumber cannot be bridged with an NSNumber allocated with a BOOL value. Even numeric bridging is not guaranteed by Apple. For example, if they changed the storage method for one of the classes (or added another subclass of NSNumber), they would not necessarily update the other side of the bridge. However, for now NSCFNumber (the "real" type here) is easily bridged.*

Simple examples of calling CFNumber functions can be found at  http://www.carbondev.com/site/?page=CFNumber

