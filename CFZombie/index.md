---
layout: page
title: CFZombie
---

Much like NSZombieEnabled, CFZombie allows you to debug over- and premature-release situations by trapping accesses to released objects. NSZombieEnabled fails to catch Cocoa classes which are really CoreFoundation classes, and CFZombie picks up the slack.

To enable CFZombie, set the CFZombieLevel environment variable to a number indicating what options you want. A good number is 3. Each bit in the number indicates an option, and the available bits are:


*0 - scribble deallocated CF memory                             
*1 - when scribbling deallocated CF memory, don't scribble object header (CFRuntimeBase)
*4 - never free memory used to hold CF objects                  
*7 - if set, scribble deallocations using bits 8..15, otherwise use 0xFC
*8..15 - if bit 7 is set, scribble deallocations using this value
*16 - scribble allocated CF memory                               
*23 - if set, scribble allocations using bits 24..31, otherwise use 0xCF
*24..31 - if bit 16 is set, scribble allocations using this value


CFZombie is (barely) documented on this page:

http://developer.apple.com/technotes/tn2004/tn2124.html

