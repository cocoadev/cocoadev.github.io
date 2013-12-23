---
layout: page
title: CFString
---

CFString is the CoreFoundation version of NSString, and is used in much the same way. Like many other basic CoreFoundation types, CFString is toll-free bridged with NSString, which means that you can safely cast a     CFStringRef to an     NSString * and vice versa.

Simple examples of calling various CFString functions can be found at
http://www.carbondev.com/site/?page=CFString
----
Panther provides two functions for escaping and unescaping XML and HTML entities:


*CFStringRef CFXMLCreateStringByEscapingEntities(CFAllocatorRef allocator, CFStringRef string, CFDictionaryRef entitiesDictionary)
*CFStringRef CFXMLCreateStringByUnescapingEntities(CFAllocatorRef allocator, CFStringRef string, CFDictionaryRef entitiesDictionary)


Unfortunately, CFXMLCreateStringByEscapingEntities is buggy, as described at http://www.cocoabuilder.com/archive/message/cocoa/2004/11/2/120728 .

To verify one of the bugs, run this code:

    
NSString* a = @"one < two";
NSString* b = (NSString*)CFXMLCreateStringByEscapingEntities(kCFAllocatorDefault, (CFStringRef)a, NULL );
NSLog( @"String \"%@\" became \"%@\"", a, b );


In Panther this yields 
    
2005-04-12 16:39:35.313 testFoundation[561] String "one < two" became "one &lt;"


Note that the final "two" is chopped off.

----
On OS X 10.4.2 at least, this particular bug seems to be fixed:
String "one < two" became "one &lt; two"
I haven't checked on any other bugs

