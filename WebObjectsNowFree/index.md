---
layout: page
title: WebObjectsNowFree
---



Hi. I'm learning the basics of Cocoa. I have a specific kind of app in mind (one that I'd eventually start developing), and it would need multiuser database access. I was happy to see General/CoreData appear, but that's apparently for single-user data files only, despite the SQL touch (General/SQLite). 

I've always heard that EOF was the way to go if someone needed real database access. However, that was part of the paid General/WebObjects package.

Now that General/WebObjects is part of General/XCode 2.1, I'm wondering how accessible it's going to be to write apps with enterprise database access. Does EOF handle the connection to databases? How difficult is it to use? Does it integrate in any way with bindings or General/CoreData?

I've found very little information on General/WebObjects. Is there any on this site? (Yes, I tried searching.)

Free General/WebObjects sounds like a major deal, yet somehow nobody seems to be talking about it. I'm confused.. *<-- the silence is a strong indicator of 'too little, too late**

I'd appreciate any pointers. -- puiz

----
As a Java person (who still has Panther right now), I'd like to know...does General/WebObjects still use the General/JavaBridge, or did Apple switch to JNI? --General/JediKnil

----
Uh, General/WebObjects has been purely java since 5.0 -- General/FinlayDobbie

*(I mean the General/ObjC interface...is there still one at all? --General/JediKnil)*


As I said, not since 5.

*Forgive my ignorance. Feel free to remove this.*


Somebody, say something!! What is this General/WebObjects deal?? Is it all all free? Is there NDA in the air??

----

No NDA I can think of... General/XCode 2.1 is available as a free download off the ADC site, and it contains General/WebObjects. I know it's been Java since version 5, but I'm also pretty ignorant about what that means... How about the Enterprise Objects Framework? Is that Java too? I know, I know, probably a very stupid question... So, is this something a Cocoa developer may want to use? -- puiz

----
Upon reflection... General/WebObjects Server is bundled with OSX Server so why not "give away" the developer version to encourage more to use it? A non-deployment license has been available to ALL ADC members for some time, I believe.

-jason

*It was only available to paying ADC members before. Don't forget that a random guy who takes five minutes to sign up for a free online membership is also an ADC member.*

The non-deployment license was definitely available to me (in April), and I have not been a paying member since 2002.

----

I am not familiar with General/WebObjects, but is the bottom line you need the deployement part to use it, and this is not free?

The General/WebObjects development version (by version I mean "license") is now free. Deployment (unlimited clients) does not seem to be free. -General/ScottStevenson

So can you develop and then use server applications that serve only one client, the server machine itself, without the deployement license? So just running a server on your machine for your own use, with address 'localhost'? (e.g. like using Instiki as a personal notebook... except Instiki does not use General/WebObjects, of course)?

From reading Apple's mailing lists, my understanding is that an unlimited deployment license is included with Mac OS X Server, and the only supported platform is Mac OS X Server, so as long as you have a Server licence (US$500) or an Xserve you're good. Everything's still Java, though, so you should be able to run on any platform if you're willing to support it yourself -- just buy yourself a copy Mac OS X Server for each deployment site to satisfy the licensing issues.   -g
