---
layout: page
title: TroubleBuildingAuxiliaryExecutable
---

Please bear with be here, because I'm sort of a noob...

I'm trying to add an auxiliary executable to my project. So I added a new Target, selecting Tool. But when I try to build it, I get two errors:

*
Command /Developer/Private/jam failed with exit code 1

Undefined symbols:
*

Can anyone tell me what these errors mean and how I can fix them? Thanks.


-Eamon

----

Most likely it's because you didn't add a Framework that is being referenced in your code. (e.g. you are using Foundation classes like General/NSString in your main function without adding this framework to the tool's target). If this is the case you need to learn how to add a Framework to a target.

http://developer.apple.com/documentation/General/DeveloperTools/Conceptual/XcodeUserGuide20/Contents/Resources/en.lproj/index.html

--zootbobbalu

----
What are you using Jam?  isn't that sort of old (I believe this is the old project builder format?).  I'd upgrade to native target as a starting point.

--John

----
Thank guys, you were both right. I didn't add a framework to my target, and I was using an old target template. So I changed it to a native template and added the framework, and everything works.

-Eamon
