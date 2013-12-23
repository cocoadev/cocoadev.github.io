---
layout: page
title: CCDFoundation
---

*oops*
----
Okay, there are going to be some naysayers for this one, but hear me out.

CCDFoundation would be a cocoadev produced alternative to Apple's Foundation distributed under GNU licence.

*It would have an all new packaging system (which is actually a cinch to do - see bottom of ChooseYourOwnPrefix) similar to or better than Javas.
*It would reorganise the class hierarchy to include some abstract superclasses between NSObject and collections for one. (gone will be the almost flat class hierarchy, in it's place will be a more structured hierarchy of classes. See NSCollection.
*It will not be hard to produce as it will be mainly proxying or using classes already developed in Foundation and AppKit, and only then where necessary.
*It will contain lots of other goodies donated by cocoadev members.
*It will take a while to produce and will be an ongoing project.
*It will adhere to XP programming guidelines as documented on www.c2.com.
*YOU can help!


This project benefits me, that's why I am proposing it. It will probably benefit you so please consider joining it.
We will also need to fight software patents to protect it. The battle is lost in the US but not in Europe:

http://petition.eurolinux.org/index_html?LANG=en

-- MikeAmy

Ok maybe this is one of my stupider ideas. What would go in this? Code people have already written? Why would that make a difference? People already just pull code in from wherever. Deficiencies between languages can be made up with bridging, this would probably just confuse people and just waste a lot of time.
Somebody give me a slap.

But what could be an answer to the namespace problem? Is there even a problem? My only doubts are in the differences between java and ObjC I guess. -- Mike

I don't Get It.  Maybe if MikeAmy gets some stuff up and running and shows us what he's after that'd work.  The "I have this great idea and we all should work on it" projects rarely get off the ground.

----
*gone will be the almost flat class hierarchy*

Is that a feature? didn't you advocate using protocols on another page, because what if we need a *new class that does exactly the same job, but isn't a subclass*.

IMHO deep class hierarchies limit re-usability, flexibility, and tie components too tightly. It also makes it much harder to counter bad design decisions -- take something like Java.io, is it three times that they have introduced entirely new class hierarchies? although still stuck with the old v1.0 API for their     System.out-object :)

----

Thanks, thats enough slaps, I've learnt my lesson, this page will go on DeleteMe. Phew, weird how the imagination can run away with itself. Normally I don't do things like this, sorry. That's the last can of coke I'll ever drink! -- MikeAmy

----
Okay, I'm late, but it looks like there's still a good idea underneath. Forget the fancy packaging and rules, we could just post and work on useful CCD objects. If you want a specific object that's not available you could either create it yourself or create a page for it (w/ ideas) and hope someone takes an interest.

CCDTextField, CCDTextFieldCell and CCDPTextView are my contributions to CCDAppKit so far. I'll base the eventual CCDSearchField on CCDTextField and I have a plan for CCDProgressIndicator (rounded progress bars).

Anyway, over time, samples and solutions could get rolled into useful CCD objects. Then if someone is looking to do something they could search for *CCD* to get a list of useful objects and they'll know if they need to create/reinvent a wheel or not.
-RyanStevens

