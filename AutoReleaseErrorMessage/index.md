---
layout: page
title: AutoReleaseErrorMessage
---

Hi,

I'm creating an application that creates a graph using the SM2D Graphview framework. The function that adds a data point to this graph is called once every second. However I'm getting this message in the run log and  I don't know what to make of it. Can someone please tell me what could be causing it? The program seems to run smoothly with no glitches anywhere but I keep getting the message in the run log.

2005-09-01 19:30:43.818 ThresholdTrack[19682] *** _NSAutoreleaseNoPool(): Object 0x3ca460 of class NSCFString autoreleased with no pool in place - just leaking

Any help is appreciated.

Anil.



----

"autorelease" can only be called inside an NSAutoreleasePool. If you're using code inside an NSThread, you need to initialize and maintain your own NSAutoreleasePool. Also, please PostYourCode

