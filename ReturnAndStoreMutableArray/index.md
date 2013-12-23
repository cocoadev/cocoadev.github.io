---
layout: page
title: ReturnAndStoreMutableArray
---



I'm trying to arrange some XmlFileReading (using the CocoaDev.com page, of course), to save and retrieve an array I have.  However, the XCode Documentation says that "the array representation must contain only property list objects (NSString, NSData, NSArray, or NSDictionary objects)."  So what can I do in order to save this information?  Thanks.

----

NSMutableArray is a subclass of NSArray, so it can be stored in a property list with no problem. When you reload it, you'll need to convert it to a mutable form with     mutableCopy. If you have custom objects, you have to implement NSCoding for them

