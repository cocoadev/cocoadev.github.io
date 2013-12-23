---
layout: page
title: NSAppleScriptMemoryLeak
---



Lately I seem to be getting a memory leak whenever i use NSAppleScript.

The code is very simple and it is as follows;

    
	[[[[NSAppleScript alloc] initWithSource:@"tell application \"iTunes\" to next track"] autorelease] executeAndReturnError:nil];


As you can see the applescript is autoreleased so this shouldn't be a problem, however whenever I run it, it leaks the following (And leaks again if ran again):

    
Dan-Sauls-Computer:~ dan$ leaks iTunesMenulet
Process 5915: 21018 nodes malloced for 2397 KB
Process 5915: 3 leaks for 144 total leaked bytes.
Leak: 0x003b7fa0  size=80
        0xa1670c38 0x003b7fc8 0x00000002 0x00000001 
        0x7374796c 0x8c8b8693 0x003b7fc8 0x00000016 
        0x00000000 0x00000000 0x00010000 0x00000010 
        0x000e0003 0x0000000c 0x00000000 0x00000000 
        0x00000000 0x00000000 0x00000000 0x00000000 
Leak: 0x003b7f40  size=48
        0xa1670cf8 0x003b7f40 0x00000004 0x00000001 
        0x7265636f 0x8d9a9c90 0x00000000 0x00000002 
        0x0038f7f0 0x00000000 0x00000000 0x00000000 
Leak: 0x0038f7f0  size=16       string 'ktxt'


Anyone have any ideas?

----
AppleScript leaks no matter what you're doing.  Even a simple compiled AppleScript Studio application will have around 3 leaks.  This is a known problem with the current version and implementation of AppleScript.  I'm not completely up-to-date on Apple's plans, but I do know they're working on fixing a number of such problems.  So, it's nothing new.  You can run "leaks" on any AppleScript Studio or ObjectiveC application with AppleScript and will likely find leaks in every single one.

