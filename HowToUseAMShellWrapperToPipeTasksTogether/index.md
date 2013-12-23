---
layout: page
title: HowToUseAMShellWrapperToPipeTasksTogether
---



I'm using amShellWrapper (I downloaded it from http://www.harmless.de/cocoa-code.php ) to launch a commandline program and I want to use NSPipe to process the output file with a second commandline utility. amShellWrapper does a really nice job of launching the first app and creating the necessary handlers, but the problem is I can't figure out how to use NSPipe to launch the second program. This is my first cocoa app so I'm not very familiar with how nspipe works. 

What I'm trying to accomplish is encoding a video file with ffmpeg then tag the output file with AtomicParsley. I'm able to successfully launch each utility separately using amShellWrapper but I don't know how to pipe them together into one action. 

I know that I need to set a standardout for the first task and plug this standardout into the standardin of the second task. but I can't figure out how its done. Any help would be greatly appreciated.
Thanks

----

Take a look at the NSTask class--if you use it instead of amShellWrapper, you may find more success.

