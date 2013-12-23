---
layout: page
title: UsingApplescriptToRunTerminalCommands
---

Getting results from a shell command in General/AppleScript:

    
 getMyWANIp = @"set theResult to do shell script (
   \"/usr/bin/curl --connect-timeout 5 -s http://www.whatismyip.com | grep 'Your ip is' 
   | awk '{print $4}'\") as string";
 	
 NSAppleScript * myScript = General/NSAppleScript alloc] initWithSource:getMyWANIp];
 NSString * wanFromApplescript = [[myScript executeAndReturnError:nil] stringValue];


----

Note: This would very rarely be a good idea.  If you need to run a shell script, use [[NSTask.  If you like, you can also use plain C methods like     system() and     popen().

If you want to download some HTTP content, take a look at General/NSURL and friends.

----

General/AppleScript is very handy, however, for running a shell command and opening it up in the Terminal. Such as SSH tasks -- build up a big long SSH command string in your Cocoa app, then use General/AppleScript to run it in Terminal.

*couldn't you do the same thing with General/NSWorkspace and ssh:// ? this would also let people use other terminal apps.*
