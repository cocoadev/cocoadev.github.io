---
layout: page
title: ObjectiveBasic
---

So this new Objective-Basic came out http://www.objective-basic.com/. What do you make of it ? All of the reviews or random comments I can find sound the same. How do you start using it !
Sure it has a manual http://www.objective-basic.com/manual.html but it's virtually useless with out a beginning tutorial or something tangible to go from. And emails seem to not reach him them at all. Any one happe to have a clue for it? 

I downloaded it, and I wasn't impressed. There's an installer, but there doesn't seem to be any need for one. It just copies a bunch of files to the /Applications folder.
And it copies *everything* to /Applications including the documentation and sample files. Very sloppy.
The documentation is very weak and unorganized; it rambles all over the place. There are no examples or tutorials on how to get started. I can't even tell if there is supposed to be a GUI tool or if you're supposed to use Interface Builder with it. The language might make sense, but the tool, in its current offering, is useless.

----

I purchased Objective-Basic, just to give the developer some encouragement.  Many of your observations are correct.  The installer is a little lame.  The documentation is incomplete, and the examples are minimal (and show no benefit of Objective-Basic over Objective-C because the code defaults to C or ObjC a lot).  However, there is a discussion forum, and the developer does listen to suggestions.  He even posted in public view all of my criticisms and suggestions for fixes and features that I emailed him in private.  Keep in mind that this is the first beta of Objective-Basic -- it is unfinished.  The developer is aware of its shortcomings and is trying to fix them.  My hope is that he'll add more automaticity to the program, i.e., creating project templates and interfacing directly with Interface Builder.  At that point, Objective-Basic will be a nice way for Cocoa newbies to transition from languages like REALbasic to Cocoa and then to ObjC-Cocoa.

BTW, I also had trouble reaching the developer at first.  He was out of the office for several days.  He's responded fairly promptly since then. 

----

A followup to my previous post:
1. Better integration with Interface Builder has been added.  Code for outlets and targets/actions is handled automatically without the need for hand-coding, just like in XCode/IB.
2. Many more example programs have been added.
3. Objective-Basic equivalents of several Foundation classes have been added.

Still a few rough edges, but looking better now as a transition tool from REALbasic to Cocoa.

