---
layout: page
title: NSArchiver
---

http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSArchiver_Class/Reference/Reference.html
----

closely related:
NSUnarchiver

more flexible: NSKeyedArchiver, NSKeyedUnarchiver (10.2 and later) - better forward/backward compatibility when you add or drop keys.

useful class method:
    
+ (NSData *)archivedDataWithRootObject:(id)rootObject


returns an NSData object representing an objectGraph of the rootObject (and therefore any objects encapsulated within that rootObject)

This is particularly useful when using with the NSDocumentDesign and the ModelViewController pattern - see UsingArchiversAndUnarchivers.

