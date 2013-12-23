---
layout: page
title: AppleCarbonApps
---

Here is a list of Carbon apps produced by Apple.


*A<nowiki/>ppleWorks 6
*DVD Player
*DVD Studio Pro
*Final Cut Express
*Final Cut Pro
*Finder
*Quicktime Player prior to version 7.0
*Shake
*iTunes


----

These aren't the droids you're looking for. Move along, move along. Here are the AppleCocoaApps.

----

And who cares....? This is CocoaDev not CarbonDev

True.  It is interesting that so many of their *best* applications are Carbon-based.
----
That what I was trying to say. Apple says not to write in Carbon, but their money-making apps are carbon.

----
If an app must still be available on OS 9, then Carbon is appropriate. Most, if not all, of these apps predate OS X. Even G<nowiki/>arageBand, a new app, is really just an adaptation of an older app. 

---

How do we know that G<nowiki/>arageBand is Carbon? Anyhow, it's not too surprising that they use it, since they've probably got a fair number of folk who've been using the Mac Toolbox for quite a while. A<nowiki/>ppleWorks in particular has brought code from long, long ago (C<nowiki/>larisWorks, anyone?), and iTunes used to run on OS 9, etc. *And now it runs on Windows too.*

---

Since when is G<nowiki/>arageBand a Carbon App? It's just a small version of Soudtrack. And Soundtrack is a Cocoa App...

----

I watched over fifty percent of the ADC sessions for 2002, and the general impression I got was Carbon developers are getting scraps in the way of UI. Carbon maybe programming "closer to the metal", but there is nothing to prevent you from taking advantage of this C API when speed or low-level functionality is needed. 

I personally prefer developing in Cocoa because I find it easier to reuse code written in Objective C. 

*It seems the other way around to me. Carbon developers have placards and relevance bars (Cocoa doesn't), and they got a brand new control framework with the H<nowiki/>IView classes. I wouldn't be surprised if Carbon controls had better built-in key support (e.g. home and end for tables, left and right for outline views) too. I really want placards! *DustinVoss

----

Carbon and Cocoa both have features that the other doesn't, and there's no sign of that changing. Apparently it's just a result of having two different teams working on them. And I checked - G<nowiki/>arageBand is Cocoa.  --DavidCatmull

----

Why was the comment made that at least QuickTime Player isn't Cocoa yet? There are a lot of things that I miss from Cocoa when using a Carbon app (when was the last time you checked spelling it iTunes?). Are there still people out there who think that Mac OS 9 is gonna make a comeback and want apps to be able to be ported back?

*I think the meaning was that whoever said it wasn't sure if it was Carbon, but [s]he was sure it wasn't Cocoa.*

----
*Are there still people out there who think that Mac OS 9 is gonna make a comeback and want apps to be able to be ported back?*

No, but Carbon & C/C++ makes dealing with multiple platforms a lot easier than Obj-C... C++ classes that hide implementation, easy as pie. IMHO, Apple dropping a procedural C API would be a mistake, every platform has one, and for a reason.

----
<rant>

So why does this have to be a Cocoa vs Carbon thing? Carbon and Cocoa are **libraries**. Both Carbon and Cocoa libraries can be used in ObjC. By the same token, a C/C++ app that is mostly Carbon can certainly take advantage of ObjC and Cocoa where the developer chooses. There's no reason why this has to be a personified **us** vs **them** argument. It's a bunch of tools, people. Use the right one for the job, learn about new tools, and figure out which ones you're most proficient and productive with. Then build your apps. Sheesh!

</rant>

--TimHart

----

I heard somewhere that Pages is a Carbon app. Can anyone confirm/deny this?

----
No, Pages is definitely Cocoa.

----

Finder is still a Carbon app. This is an embarrassment. Couldn't it have a Cocoa 'face and use the Carbon where back-end necessary?
No. Apple are too busy in consumerland, i.e., tuneland, to concern themselves with this doddering old fart of a program.

----
Actually, Finder was considered the "sacrificial lamb" that was in some ways a giant test bed to prove that Carbon provided enough functionality to write a complicated modern application with. And please don't blame any bugs in the Finder on Carbon. The bugs it has are bugs in the overall design, not any problem with Carbon vs Cocoa.

----

Oooor they could just make it a good Carbon app, and not have to rewrite huge swaths of code for no reason. --DavidSmith

----

Oooor they can buy out CocoaTech and use PathFinder

----

The best apps will use the best solution. If something is done better in carbon, then use carbon. If something is done better in cocoa, then use cocoa. If the app does both these things, then use both. Simple as that. -- Louis Klaassen

