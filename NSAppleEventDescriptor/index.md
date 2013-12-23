---
layout: page
title: NSAppleEventDescriptor
---

NSAppleEventDescriptor is a wrapper for Carbon's     AEDesc type. Each NSAppleEventDescriptor has a **descriptorType** and **data**. The data may contain additional nested     AEDescs.

An     AppleEvent type is an     AEDesc, as is each parameter of an Apple Script command.

Most NSAppleEventDescriptor methods are convenience methods for dealing with the data of various descriptor types. They may not always be applicable. 

See also:

*Class documentation at http://developer.apple.com/documentation/Cocoa/Reference/Foundation/ObjC_classic/Classes/NSAppleEventDescriptor.html
*    AEDesc documentation at http://developer.apple.com/documentation/mac/IAC/IAC-98.html
*Descriptor types at http://developer.apple.com/documentation/Carbon/Reference/Apple_Event_Manager/Reference/reference.html#//apple_ref/doc/uid/TP30000134-CH4g-C006985


----

KFAppleScriptHandlerAddtions (http://homepage.mac.com/kenferry/software.html#KFAppleScript) has some routines for converting back and forth between descriptors and cocoa objects.

    AEVTBuilder is another Cocoa glue classes which build NSAppleEventDescriptor.


----

BEWARE:     -descriptorAtIndex: uses a 1-based array!! Does anybody know why this is? Reminds me of the good (not really) ol' BASIC days. --Kevin

The *actual* reason why is because the original Mac OS toolbox was based on Pascal. Pascal arrays stored their actual length at position 0, producing two noteworthy side effects:

* you can measure the length of an array in O(1) time 
* items are referenced in lexical order. E.g., "item 1 is in slot 1". 

The Cocoa Apple Script code was just thinly veiled shims around the older System 7 implementation -- and the API still betrays that heritage. HTH -- MikeTrent

P.S., I've left the other guesses behind for your amusement:

----

*I guess the method should have been named     -descriptorAtAppleScriptIndex:. AppleScript uses 1-based array indices, for the same reason BASIC does. -- DustinVoss*

*and that reason is? "no good one" is all that comes to my mind.*

Because before someone told you that the nth item is in the n-1th position, you may have been confused... the problem is of terminology, because while everyone says we access the array item at an index, we, having learned to code with C's close to the hardware semantics, actually access the array item with an offset, thus the first object is at an offset of zero etc.  In other words: real world people talk about the third floor when a programmer would say, "but you climb only two sets of stairs to get there," leading to confusion.  This is why BASIC and Applescript, being designed for real world people, use base 1 arrays/lists.

*I use the elevator... ^_^*

Actually, in Britain, the third floor would be one up three sets of stairs. We have ground floor, then the first floor on top of that. Perhaps this zero-based index stuff was invented by a Brit? ;)


----

Pascal did not store the actual length of an array at position [0].  Many versions of Pascal did that for short strings, but Pascal arrays in general allowed you to specify both the minimum and maximum, so the minimum index was dependent on the definition of the array, and was often chosen to be 1.  For example:

var a : array[1..20] of integer;

Because people normally count from 1, Pascal programmers normally based their arrays from 1.  And that Pascal heritage is indeed why the AppleScript descriptorAtIndex is 1-based.

For strings, the definition like:

var s: string[20];

was functionally similar to

var s: array[0..20] of char;

With Ord(s[0]) being equivalent to Length(s).

