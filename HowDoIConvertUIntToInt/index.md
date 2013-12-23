---
layout: page
title: HowDoIConvertUIntToInt
---

I have a strange case that I'm dealing with. I'm using the DVD framework, which returns UInt32 and UInt16 values for the time codes when you ask it the current title, frame, time, etc. I've never seen these before, and my Google searches don't reveal any information about them. My question is: how do I convert these types to a standard int? My reason for this is that I need to be able to feed the converted values into NSNumber and other methods that require an int to start with. Any ideas? Right now, when I grab the number for the current title of the playing disc, which is 1, NSLog  displays it as 65536.

----
http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSNumber_Class/Reference/Reference.html

#//apple_ref/occ/clm/NSNumber/numberWithUnsignedInt:

----
Just double click on the "never seen before symbol" while holding down the command key and Xcode will show you where the symbol is defined. 

    
typedef unsigned char                   UInt8;
typedef signed char                     SInt8;
typedef unsigned short                  UInt16;
typedef signed short                    SInt16;

#if __LP64__
typedef unsigned int                    UInt32;
typedef signed int                      SInt32;
#else
typedef unsigned long                   UInt32;
typedef signed long                     SInt32;
#endif


--zootbobbalu

----
Obviousness:

* PostYourCode
* Did you try     = ?


----

*[NSNumber numberWithUnsignedInt:foo];
*[NSNumber numberWithUnsignedLong:longFoo];

