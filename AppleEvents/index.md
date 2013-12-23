---
layout: page
title: AppleEvents
---




AppleEvents are high-level, semantic messages designed to allow for collaboration between programs. AppleEvents enable interapplication communication (like SOAP).

Apple's OpenScriptingArchitecture (OSA) uses AppleEvents to script and record applications using a simplified programming language (AppleScript).

AppleEvents were widely used in AOCE & OpenDoc and wildly praised in Orfali & Harkey's "Essential Distributed Objects Survival Guide". Chapters 3-6 of "Inside Macintosh: Interapplication Communication" describes AppleEvents and the AppleEvent manager. 

"The Open Scripting Architecture: Automating, Integrating, and Customizing Applications (Cook & Harris, 1993)" [http://www.cs.utexas.edu/users/wcook/papers/AppleScript/AppleScript95.pdf] provides a very good overview of the original AppleEvents/OSA/AppleScript architecture.

AppleScript is built around AppleEvents

The AppleEvents API (AE.framework) is part of the ApplicationServices umbrella.

The following developer docs provide more information on AppleEvents and how to use them:


* http://developer.apple.com/documentation/AppleScript/Conceptual/AppleScriptX/index.html
* http://developer.apple.com/documentation/AppleScript/Conceptual/AppleEvents/index.html


----

You can build and send AppleEvents with NSAppleEventDescriptor, Carbon functions (see AECreateAppleEvent, AEBuildAppleEvent, AESend, AESendMessage), the high-level ObjCAppscript bridge (10.3+) or ScriptingBridge (10.5+).

----

*You can also receive AppleEvents as NSAppleEventDescriptor<nowiki/>s by registering with NSAppleEventManager. NSApplication does this to turn     odoc,     rapp and other standard AppleEvents into delegate messages, or invoke default behaviour if needed.*

----

A C program can use AEMach.h to receive AppleEvents, however the initial oapp/odoc event will not be received there. As of Lion it's fetched from a separate mach port using the private _LSCopyLaunchModifiers function the first time [NSApplication run]/RunCurrentEventLoop is called. Thus there's no way to parse it except to use Cocoa or Carbon.

