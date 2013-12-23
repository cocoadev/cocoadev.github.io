---
layout: page
title: AutoReleasePools
---

Hello, 

I am a Cocoa newbie and I'm wondering why the default code that's given in a cocoa application in Xcode creates and deallocates an NSAutoreleasePool. Do you always need to create an NSAutoreleasePool even if you never explicitly use the NSAutoreleasePool object that's been initialised?  I'm looking through "Cocoa Programming for Mac OS X" and it does the same thing in the 'LotteryEntry' exercise -- creating an NSAutoreleasePool 'pool' object even though that object is never explicitly used, as far as I can see.

Thanks for your help.

----

An NSAutoreleasePool is implicitly used whenever something is autoreleased.  The frameworks always assume autoreleasing is allowed, so it isn't safe to make *any* cocoa call if an autorelease pool doesn't exist.   For example, the line     [NSArray array] will leak memory if there isn't an autorelease pool set up.

You can think of autorelease pools as being on a global stack.  When an object is autoreleased, it's added to the topmost pool.  When that pool is popped off the stack (i.e. when it's deallocated) it releases every object it contains.  This is the mechanism by which an autorelease is a 'delayed release'.  If there is no pool on the stack, autoreleased objects drop to the ground, so to speak, and are never released.  They leak.

When you write an application using the "Application" template, autorelease pools are created and destroyed for you automatically.  You don't have to worry about them unless you're using multithreading or optimizing memory usage.  However, it's necessary to explicitly set up a pool if you're writing a command line tool.  The Xcode 'Foundation Tool' template already has the code you need.

----

I'm going down to the local autorelease pool for a quick swim, anyone care to join? *not me. you never know what's been released in there...* lol

