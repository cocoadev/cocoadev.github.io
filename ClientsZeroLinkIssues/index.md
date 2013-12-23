---
layout: page
title: ClientsZeroLinkIssues
---



A friend of mine tried my beta program on his machine, and got this error in his terminal:

    Last login: Sun Apr 24 20:06:29 on console
Welcome to Darwin!
/Users/jeffg/Desktop/Assignment\ Planner.app/ConteJeffs-iBook:~ jeffg$ /Users/je/Assignment\ Planner; exitanner.app/Contents/MacOS 
dyld: /Users/jeffg/Desktop/Assignment Planner.app/Contents/MacOS/Assignment Planner can't open library: /System/Library/PrivateFrameworks/ZeroLink.framework/Versions/A/ZeroLink  (No such file or directory, errno = 2)
Trace/BPT trap
logout
[Process completed]

I thought the ZeroLink had to do with the compiling of the program, and is part of XCode.  Since my friend doesn't have XCode (as he's not a developer), the program wouldn't run for him.  Does anyone know why this could be happening?

----

ZeroLink has to do with the linking of the program. ZeroLink'd applications won't run on any computer other than the one it's built on.

*You will have to clean your target and build it in Deployment mode.*

