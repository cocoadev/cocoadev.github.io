---
layout: page
title: IsXcodeSignificantlyFasterWithDualGeeFive
---



I use XCode all the time, and my current project has about 13 targets within it (mostly plugins). Every time I open the project, Xcode takes quite a while to finally let me do something in the window (a few minutes). Then, when I add or remove a file from a target, it sits for about another minute or so until I can continue doing whatever I was doing. I have an eMac 1Ghz with 640MB (all I could afford!), but am wondering how Xcode would compare on a dual GeeFive. I'm sure it would be faster, but does the dual processor make much of a difference? Thanks -KevinWojniak

XCode uses dual processors when building, so that would go much faster. I'm not sure how much the rest of it is threaded. --DavidSmith

Seems pretty quick to me (I have a 1.8D / std 512MB) although I'm a complete newbie so don't have much to compare it with. I'm writing a smallish size project (about 12 classes, 2 nib files and a library set) and XCode takes around 5 seconds to start up, and can vary of how long compilation takes: from instant to about 5 seconds. --ArthurPeake

Don't forget that the hard drive is faster and the system bus is faster. The first alone will make loading of projects snappier, compiles faster and linking faster. The latter will be very useful when you have compiles that flood the bus of an old G4. 

Personally I found the difference between a G4 1ghz Albook and a Dual 1.8 very large, possibly on the magnitude of 4x for compiling in deployment and 2-3x with development enabled. As for massive compiles (like compiling one of my apps) it would take 1 min to compile from clean on my PowerBook and about 12-14s to compile from clean on the G5. -- MatPeterson

In my experience -- provided you don't use the functionality -- turn off the code completion/auto-indexing mechanism. On my PB12" & my quicksilver, xCode borders on completely unusable with the code indexing turned on. With it off, however, xCode is as zippy as I could ask for. Of course, YMMV and if you *need* code completion you're SOL. --ShamylZakariya

I agree with Shamyl, the code completion absolutely hammers Xcode. I have it switched off on all my rigs. -- MatPeterson

----

Well, after many months of using Xcode on a dual G5 PowerMac, I can tell you that it definitely is much faster with the dual processors. Also, giving yourself more RAM (I'm up to 2GB) and a faster hard drive (just upgraded to a 10,000 rpm Western Digitial) will help speed up compiling much more!

----

I've had this machine speed discussion before. In at least one company I worked for, when I said developers need "faster machines than the data entry clerks", the guys in desktop support group focused solely on processor speed. I never did really succeed in convincing them that programming was mostly opening, reading,  holding in memory (all at once), and writing a bunch of little files over and over again. The process is I/O bound, and a fast hard disk with low seek and latency times, along with plenty of memory to allow the O/S to cache as much as it can, will contribute measurably more to improved performance for a programmer than a minor change in CPU clock speed. In the best of all possible worlds, the source code and build output would be on a disk with a separate I/O channel from the system and applications disk.

