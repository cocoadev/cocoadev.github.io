---
layout: page
title: NSApplicationLoad
---

This needs to be called from carbon applications wishing to integrate with cocoa to make sure that cocoa  is initialized properly

----

This thingie is painfully underdocumented. :(
NSApplicationLoad seems to set up NSApp so that some things work as expected even though we're in a Carbon Event Manager run loop rather than a standard Cocoa environment -- for example, [NSApp windows] returns a set of wrapper objects rather than an empty array _most of the time_. Also, it seems to try and wrap the Carbon run loop with an autorelease pool, although doing so seems to trigger an error like the following most of the time (in a Cocoa plug-in loaded via mach_star into a Carbon application):

    
2006-10-23 19:09:28.872 DVD Player[3624] WARNING: _wrapRunLoopWithAutoreleasePoolHandler got kCFRunLoopExit, but there are no autorelease pools in the stack.


If somebody can enlighten me, please do so :( -- EmanueleVulcano aka millenomi

----

Have you tried loading your Cocoa plugin into a test Carbon application (without using mach_star)?
This problem may have something to do with mach_star and/or DVD Player.

----
Some googling on the message seems to imply that this is not entirely abnormal for apps mixing Cocoa in Carbon, even doing it normally without odd plugin injection systems, and so you might just ignore the thing.

----

I found that calling [NSImage TIFFRepresentation] caused some warnings (for example: *_NXCreateWindow: error setting window property*) if I called it in a non-GUI command-line tool. Calling NSApplicationLoad() made them disappear, so hopefully this is the right thing to do.

