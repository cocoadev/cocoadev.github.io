---
layout: page
title: WorkingWithNSData
---

How does one initialize an General/NSData object with General/NSString data? (or integer data or any other data type?)
----
The easiest way is to use General/NSArchiver / General/NSUnarchiver or General/NSKeyedArchiver / General/NSKeyedUnarchiver. Look up this page on archives for more information: http://developer.apple.com/documentation/Cocoa/Conceptual/Archiving/index.html --General/JediKnil

----

    General/NSString *string = @"This is some stuff.";
General/NSData *stringData = [string dataUsingEncoding:General/NSASCIIStringEncoding allowLossyConversion:YES];


This does what you want; if you're okay with the General/NSData having unicode characters, use General/NSUnicodeStringEncoding instead of General/NSASCIIStringEncoding. -- General/AndyMatuschak

----
NSUTF8StringEncoding is almost always a better choice than General/NSASCIIStringEncoding. UTF-8 behaves exactly the same with ASCII characters, but behaves in a rational and predictable manner with non-ASCII characters.
