---
layout: page
title: DistributedComputing
---

A collection of information on how to implement distributed computing with Cocoa, i.e. write applications that solve a problem by dividing into many tasks, each of which is solved by one or more Macs.


----


The official Apple approach is (was?) **Xgrid**:

* "*Xgrid is a proprietary software program and distributed computing protocol developed by the Advanced Computation Group subdivision of Apple Inc that allows networked computers to contribute to a single task.*"
* http://developer.apple.com/library/mac/#documentation/MacOSXServer/Conceptual/Xgrid_Programming_Guide/Introduction/Introduction.html


The **Xgrid Programming Guide** was last updated in 2007 so it's probably safe to say that Apple is considering that  as no longer appropriate.


----


A low-level Cocoa approach using distributed Cocoa objects with **NSConnection** can be found here: DistributedObjectsSampleCode.


----


**libGlass** is an actively developed library for distributed computing. It's written in C++ and widely portable. Glass is POSIX compliant, and should work with any C++ compiler. 


* http://sourceforge.net/projects/libglass/
* https://sourceforge.net/apps/mediawiki/libglass/index.php?title=Main_Page

