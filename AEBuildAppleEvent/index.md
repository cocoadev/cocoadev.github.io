---
layout: page
title: AEBuildAppleEvent
---

** AEBuildAppleEvent()**  is a Carbon function for creating an Apple Event, including parameters, in a single call (available in OS X 10.0 and above). The event's direct parameter and other parameters can be included in the arguments to the call in a printf-like string format either as literals, or with substitution operators (think "%d", except this uses "@" and "@@"). 

AEBuildAppleEvent is a replacement for using a series of Apple Event Manager calls to create an event ( AECreateDesc, AECreateAppleEvent, etc.)

For an overview of the function, a bit of sample code, and some constants, see Apple Events Programming Guide http://developer.apple.com/documentation/AppleScript/Conceptual/AppleEvents/index.html "Creating and Sending Apple Events". Constants are described fairly well in Appendix B.

For a detailed description of the function, including the paramsFmt string syntax, see http://developer.apple.com/technotes/tn/tn2045.html "AEBuild*, AEPrint* and Friends" (keep reading to get past some forward references). The note describes how print descriptors of events sent by regular AppleScript by attaching gdb to Script Editor. This is a good way to get meaningful examples of the special string format.

For general AE-creation sample code (but not using AEBuildAppleEvent): http://developer.apple.com/samplecode/MoreAppleEvents/index.html

For the bulk of AE functions and constants, see http://developer.apple.com/documentation/Carbon/Reference/Apple_Event_Manager/index.html (reference of last resort--constants are listed without order or definition).


** Context: ** As of OS X 10.4, Cocoa does not provide a mechanism to send individual Apple Events. You can either:

* use AEBuildAppleEvent and AESendMessage, or
* build it piece-by-piece with NSAppleEventDescriptor, convert it, and use AESendMessage, or
* include a compiled AppleScript (see NSAppleScript ) in your bundle, or 
* build an AppleScript with NSString<nowiki/>s (and compile on the fly), or
* use AEVTBuilder, an Objective-C library by MikeAsh, or
* use ObjCAppscript, an easy-to-use high level Apple event bridge for Objective-C.

It is possible, but complicated, to pass arguments to the precompiled AppleScript from your code ( http://developer.apple.com/qa/qa2001/qa1111.html ).

*This context section probably belongs some other page; I'm not sure where.*

----

AEBuildAppleEvent is rather particular about the string. If you use AEPrint output as a guide (above), add corecions around the values. (see also http://lists.apple.com/archives/Applescript-implementors/2002/Mar/msg00038.html ). The good news: the AEBuildError return actually tells you the character position in the string it has trouble with.

----

The easiest way to find AppleEventCode is to find the .scriptSuite file for the application. For example, to find out QuickTime's playSelectionOnly command, go to     ./QuickTime.app/Contents/Resources/QTPSuite.scriptSuite. Simply open the file in your favorite text editor and search for what you want.

