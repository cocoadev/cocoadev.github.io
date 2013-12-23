---
layout: page
title: NSViewCrash
---

Hello,
This stack trace is giving me the willies. I have no custom window, so this code does not point to any of my code...?
The only thing that might be related is my NSScrollView subclass which overrides -hitTest: to trap mouse events.
Has anyone else come across this type of error. The error is repeatable, but I can't link it to any of my code yet.
Could it be a NIB bug?

    
Thread 0 Crashed:
0   libobjc.A.dylib           	0x908311e8 objc_msgSend + 0x8
1   com.apple.AppKit          	0x92e026e0 -[NSWindow sendEvent:] + 0x64c
2   com.apple.AppKit          	0x92df583c -[NSApplication sendEvent:] + 0xebc
3   com.apple.AppKit          	0x92dfdc54 -[NSApplication run] + 0x240
4   com.apple.AppKit          	0x92eba2b8 NSApplicationMain + 0x1d0
5   com.apple.myCocoaApp      	0x000030c4 main + 0x38 (main.m:13)
6   com.apple.myCocoaApp      	0x00002b08 _start + 0x198 (crt.c:267)
7   dyld                      	0x8fe1a558 _dyld_start + 0x64


Advice, Input?
Thanks,
Jeremy J

----

What is the actual error given (it's not included here). It's likely a signal 10 or 11. Most commonly, this is due to a autoreleased (or otherwise prematurely released) object that no longer exists. 

----

It WAS a bad access error. But the problem went away when I fixed a buggy hitTest implementation (not the NSScrollView one either!) that failed to return any value at all. Is it my compiler settings (which are customized 6 ways from sunday) or does GCC really not warn you of obj-c methods that fail to return a value? I found two other methods in which I simply did not put a return VAL at the end, and they compiled fine with no warnings. That seems too insane to be the default setting!

----
Add these to your warnings flags:
    
-W -Wall -Wno-unused-parameter

The first two turn on basically every warning that the compiler has. The last one disables unused parameter warnings, which are pretty useless in Cocoa apps where unused parameters are all over the place.

