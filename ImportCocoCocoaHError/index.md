---
layout: page
title: ImportCocoCocoaHError
---

I have downloaded the source of an application from sourceforge with the intention to make some slight improvements. I tried to build it but I get over a hundred errors along the lines of file Cocoa/Cocoa.h doesn't exist.  I tried to remove the references in the project and then add framework->Cocoa.framework back into the project, but it still wouldn't file Cocoa/Cocoa.h... other builds find it fine, it seems to be just this app... Anyways I may create a new project and import all the files to make it work, but any thoughts on why this would happen?  (the app is FeedMe... the example app for IconaraDOM, I am adding a window and browser view... or would if it would compile...)

----

Did the #import statement use quotes or angle brackets? It should be the latter:     #import <Cocoa/Cocoa.h>

*Well, I've had a glance at the FeedMe source and apparently that isn't it, unless you've changed something. Most things appear to be imported from     feedme.h so make sure you haven't done something to that file.*

----

I'm the author of FeedMe. I'm not so very proud of the code, so please don't look at it. It was written, and then added to some time later, and added to some time later again, and now the controller is just code bloat.

It was meant to be a nice example application using good design patterns, but I ended up trying to do something that was useful for me, and do it in too little time, with too little forethought.

-- TheoHultberg/Iconara

----

Yes the import statements use <>, yes the controller code is extremely ugly and after attempting to refactor I seem to have broken something... sigh... I just want a RSS reader that gives me my news summaries and also is pluginnable for sites that aren't RSS.  I also want a window (Dock Menu-y interface bugs me)  Maybe I'll try hacking it some more, but as it stands it all fell apart.

----

Oh, please, to say that the controller code is *extremely ugly* is a bit over the top. I'm the first to admit that it's not perfect in any way, but it's quite well documented and the code is clean. What makes it ugly is that it has too many responsibilities, and it is not well designed. I would love to help you write your RSS-reader, but drop the attitude.

May I suggest that you mail me (you can do that from SourceForge page or get the e-mail from the ReadMe), and I'll send you some updated code. I have refactored the cache, and the feeds, but it's not finished. It handles RSS, but could easily be extended to handle any feed, as long as a mechanism for discovering the format is added. You are very welcome to rewrite things.

If you're into writing it all by yourself I still encourage you to use the IconaraDOM framework. It will simplify the XML-handling bits greatly.

--TheoHultberg/Iconara

----

Alright... I'm sorry... I can't say every line I've ever written has been up to my standards again next week let alone the age of this code base... I like to divide my controller class in two, one that only talks to Foundation/Suporting model classes, and one that only talks to that previous object and AppKit.  I find it reduces the amount of times methods start with declaring and setting the same variables, and makes over all method length closer to one screenful.  I apologise for applying my personal taste to the code, which was in fact, readable enough for me to copy and paste out the relevant bits from the methods I wanted in under half an hour... I only now realise I think I am doing something bad with multi-threading because I don't normally use threads, and this app does.  At any rate, this wiki node has gotten a little off track... Can anyone think of a reason that XCode wouldn't find the include for Cocoa when the framework was included.... could it have something to do with a PreCompiledHeader? and if so how would I go about recompiling a pch for the project if it compiled itself with problems?

*cleaning the project should get rid of the precomp*

I tried clean all targets but that didn't seem to get rid of the error either.... Could it have something to do with it being a .pbproj not a .xcode, while I am using xcode?

----

Apology accepted. It's not impossible that the problem is that it's a PB-project, dunno, have you tried to upgrade the project? Perhaps the box for "precompile header" is ticked for     feedme.h, and xcode not getting it? I know that I have built the app with XCode though. Anyway, don't waste your time on that project, start a new one with the code you want instead.

--TheoHultberg/Iconara

----

That's what I've done... I just hoped to cover the issue on CocoaDev in case someone else had an answer, or the same problem. (To those creating a new project and mass importing files from an old one... don't forget to check the box to include each one in the current target's build sequence!)

