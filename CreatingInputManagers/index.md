---
layout: page
title: CreatingInputManagers
---

CreatingInputManagers (from scratch)

Hi, I'm looking for a crash course on making an input manager from scratch. I've looked everywhere, but no tutorial seems to explain the process right from the start. What kind of Xcode project do you use? How do you access the outlets of a class or the methods of a class? Where do you start!

If you're wondering as to why I need to make the input manager, I want to rotate the main view of Rondo http://www.fracturedsoftware.com/rondo/ so that I can look at the music horizontally, like you read music normally, rather than vertically.

Any help is appreciated. Thankyou!

----
Probably the best starter resource on these sort of "forced plugins" is from the creater of SIMBL, MikeSolomon. You can find his Cocoa Reverse Engineering guide at http://culater.net/wiki/moin.cgi/CocoaReverseEngineering . --JediKnil

----

Be very careful with InputManager<nowiki/>s.  Remember that NSInputServer is only used by Cocoa apps, and it's very easy to screw things up subtly.  Also, since you seem to be doing synth stuff, you might want to pay attention to the fact that you're going to potentially be causing a bottleneck by forcing a task on cpu1 to wait for a reply form your input server running on cpu0, even if the task you're blocking wants nothing to do with synth stuff.

