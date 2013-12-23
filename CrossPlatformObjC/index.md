---
layout: page
title: CrossPlatformObjC
---

Despite the loss of the YellowBox, we (Cocoa developers) do have some options for writing CrossPlatform code.


* GnuStep
* Writing your own model classes (not as hard as it sounds)
* ModelViewController


GnuStep is described better on its own page, but in short it's an OpenSource implementation of the OpenStep APIs. It runs on most UNIXen, so if you don't need to run on Windows, but need Linux, this may be a good choice.

Writing your own model classes really isn't as hard as it sounds. I've got enough of Foundation reimplemented for my own purposes that I'm not really missing the NS* classes (not on that level, anyhow; and for the UI, I'm using the GLUtilityTookit). One problem with writing your own model classes is that it can be pretty tedious, and you want some rigorous testing, and that can add development time that you wish weren't added.

The MVC model for coding is nice for a lot of reasons; for CrossPlatform code it's nice because it can make it easier for you to port your code to other systems; if you can keep your platform-specific code in one "place," it makes it far easier to port it to another platform.

It would be wonderful to have **write once compile everywhere** back with Cocoa, but it's not currently possible, and you probably shouldn't hold your breath waiting. In the meantime, you can cut your losses by making sure you code with portability in mind.

----

Well, you know. Apple's Objective C is not the only one out there. A cursory examination of the objc newsgroup's FAQ reveals that David Stes has created a compiler, the POC, that will work on any platform. It works by compiling the ObjC into C and then adding in some compatibility libraries. His compiler also adds closures, which is a nice feature. 

It's unfortunate such a useful thing is maintained by such a prick. David Stes is a consumate Apple hater and a serious troll in objc. Most people who stay there for any length of time filter him out as white noise rather quickly. Because of his attitudes, he keeps his POC incompatible with Apple's code (which IS unfortunate because his compiler and a bit of elbow grease are all that keep Cocoa from running on windows in the minimal amount of time, through MS's own IDE even!)

However, if all you want is the flexibility of Objective C and C++ compatibility, and don't care about the Foundation or AppKit, then the POC might easily suit your needs.

-- DaveFayram

True, the POC (anybody else see "piece of crap" anytime they read that unfortunate acronym? :| ) is an option. Personally, I'm sticking with gcc because it's what I know, it's available just about everywhere (even Windows! MinGW), et cetera. So clearly, the compiler isn't the issue, it's the availability of the frameworks for other platforms--basically zero, unless you can use GnuStep.

So that's why I'm writing my own frameworks. And who knows, maybe I'll be nice and give source or binaries away someday :) -- RobRix

