---
layout: page
title: TollFreeBridging
---



General/TollFreeBridging is what allows you to take General/CoreFoundation types and typecast them to Cocoa types (and vice versa).

So, if you have to call a General/CoreFoundation function that takes a General/CFString, but you have an General/NSString, you can just pass it your General/NSString with:

    
General/CFStringRef myCFString = (General/CFStringRef)someNSString;


And if you need to get an General/NSString from a General/CFString, you can do this:

    
General/NSString* myNSString = (General/NSString*)someCFString;


see also: General/HowToCreateTollFreeBridgedClass

See which classes are General/TollFreeBridged.
