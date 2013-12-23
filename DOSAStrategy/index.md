---
layout: page
title: DOSAStrategy
---

To understand DOSA see DOScriptingArchitecture.  The basic concept is to use Cocoa's built-in DistributedObjects APIs to implement scripting for applications on MacOSX with less code, more convenience, regularity, cross-language than the OpenScriptingArchitecture (AppleScript), and many other benefits besides.

Some key pieces that I see as requirements and am working to build are:

0) DoIt : a command line unix tool for doing things with DistributedObjects... Even without DOSA in mind it's a useful tool for people using DO... and with DOSA in mind it gives access to DO from anything that can launch a unix process.

1) applications that vend interfaces: I am doing this currently with all of my personal applications, some of which I will be making public and open source in the near future. (Currently I have a notepad based on the code I donated to NSTextViewAsWiki called WikiPad which you can script access to wiki nodes etc., an rss aggregator called NewsAnchor which allows you access to feeds and articles, and soon VendingMachine...see 2)

2) giving DOSA interfaces to Apple's and other currently scriptable apps: I am working on an application I call VendingMachine that will accept plugins representing interfaces to applications I can't actually get inside of to vend from, which will vend objects for them. (like iTunes, Safari, Mail...)

3) An OSAX for AppleScript-ers to be able to access DistributedObjects, and maybe an NPAPI plugin so DashBoard gadgets can too (this is one I am excited about).

4) A C API that wraps the relevant bits of the ObjCRuntime so carbon developers can vend object interfaces.

----

Poll: This is a waste of time (Yea: 2, Nay: 6)

----

This is a great idea. It would be much easier to use and integrated for us Cocoa developers than Applescript is. Indeed, we don't need the heavy and complex Applescript machinery since Objective-C already has a native way to describes rich programatic interfaces and to access them from other applications (with DO). 

----

DOSA seems more for programmers than users. It is too low-level and version-dependent, and would suffer worse documentation problems than AppleScript does. At least AppleScript has dictionaries, DOSA doesn't even have that. And if you make a separate document for DOSA, well, you can and should make a separate document for AS as well.

----

Your DOSA reminds me of the OMG's IDLScript specification. The idea is to have a scripting architecture on top of the CORBA distributed object system, just like DOSA aims to be a scripting architecture on top of the Cocoa's DistributedObjects system. Several things are based on the IDLScript spec, including CorbaScript (see http://corbaweb.lifl.fr/CorbaScript ), an interactive scripting language which let one manipulate CORBA distributed objects.

----

Well the protocol definition you put in the bundle should actually be one you use on your code... thus they stay together while versioning, and a well-written protocol will be just as readable and more predictable than an AppleScript dictionary... One other thing I want to add to the DOSAStrategy is a command line tool: DoIt... on the command line you would type: "DoIt com.apple.iTunes playPause"... and it would [[NSConnection rootProxyForConnectionWithRegisteredName:@"com.apple.iTunes" host:nil] playPause];  This could instantly be used by applescript, perl, and all the others until they get their own native DO API.

*This is exactly what I meant by low-level and version-dependent. DOSA promotes scripting terms that are exactly the same as the app's internal structure. If you change the implementation of a feature, you have to change the DOSA terms. This breaks backwards compatability. AppleScript intentionally forces an abstracted object model that is distant from the app's internal object model. This means it is less likely to need to change as you change your app's implementation. Developers don't have so much of a problem with this, but users do. It reminds me of Microsoft's scripting: low-level and fragile. I suppose DOSA would be fine for a quick-n-dirty scripting implementation, but the problem with quick-n-dirty is it is never upgraded to robust-n-good. �DustinVoss*

Actually no... just declaring the protocol doesn't require it do be the actual methods that do the heavy lifting.  It's called an interface... it's separate from implementation.  Because it's an interface that isn't even related to the class, I can move it to a different object that works upon a completely different model, but you don't see it.  A well-thought-out interface will present the user with the methods they need and if new features need to become available you can add new methods... and the reverse is possible by commenting that a method is becoming obsolete.  This is exactly how it works in the rest of the Cocoa frameworks, and even the AppleScript APIs for the Finder and many other apps have had their scripting interfaces migrated.  The point of a protocol is that it is an interface completely devoid of implementation, that can be adopted by any class without regarding to its inheritance hierarchy.  I think this has the benefit of actually enabling application builders and scripters to build a more reliable interface for interaction.  Have you tried to script using AppleScript for a variety of programs lately? the dictionaries tell you almost nothing about how to script, and they are so much work to create and maintain as a developer that the majority of current applications (not released by Apple) either don't have one or it is no longer in sync with what the app does anyway.  Creating a scriptable application should be easy for developers, to encourage wide adoption, which means more workflow options for scripters.  It should also be predictable for scripters, something AppleScript is not.  Each application has to re-implement functions for the many kinds of list access in AppleScript, many of these implementations are either buggy, or written into the dictionary without actual support.  Each application has to create support for the AppleScript object model, why should non-language developers be working on object models seperate from their implementation language.  Each application has to parse the events sent to it for four-letter codes and translate them into names of data types and operations... These are all things that take lots of work, extra-lines of code (tantamount to extra bugs), and things that I just don't want to waste my time on.  DOSA gives you the array and lets your language work on it... DOSA uses the object model of ObjC, the main implementing language on the Mac at this moment, which is compatable and bridged to the object models of other languages like FScript, python, and ruby (due to exposure of the ObjCRuntime API in C)... A DOSA application has to do no extra work to parse a scripting event to get the arguments and data types... nor does it require work done on an interface that isn't part of the code (leading to the easy possibility of the interface NOT matching implementation).  Yes, it means that someone could give straight access to the model of their application and treat the protocol as liquid rather than set in stone... Similar things are possible with AppleScript.  You are a good developer, so I am sure you won't let that happen just because now it's *easy* to make a scripting interface... The only reasons I can think at the moment of to oppose this are (A) masochism (B) jaded... (having done all the work on an AppleScript interface it's no longer necessary) or (C) resistance to multiple ways to do things.  Actually (C) is semi-valid... you split the developer community and wind up with things designed to work together in clumps, but not all together (Gnome/KDE, going further back Netscape/IE) .  This is why I see an AppleScript DOSA interface as one of the primary goals.  I already have an implementation of DoIt, so AppleScript right now can call it with "do shell script"; but a native OSAX interface is on the way.

*
Many of your points are good, especially the one about buggy element accessors, and your earlier reference to using DOSA for Dashboard gadgets. The flattery also helped. :) However, in response to your rebuttal of my response, I offer these three points:

1) Yes, the dictionary tells you almost nothing about how to script. That is because dictionaries were never meant to be the primary reference. They were always to be intended as a quick reference backing up a real document. But developers did not bother to do the real document, because they had the dictionary. DOSA will also need separate documentation. The whole dictionary issue is a wash. Both AS and DOSA will need separate documents, but at least AS has a dictionary, though we might have been better off if it didn't.

2) It will be too tempting to tie the DOSA interface directly to the app's architecture. DOSA won't require that, but it will encourage that. Developers almost always choose the easiest way to do something�it's that whole WorseIsBetter thing.

3) DOSA's AS support layer had better be good. Remember that people script in OS X not only to encapsulate repetitive tasks in a single app, but also to tie specialized applications together in a work-flow. DOSA will never displace AS, so if a DOSA app doesn't play well with other AS apps, people won't use it.
*

Re: (3)  There are two parts to integrating DOSA and OSA... calling AppleScript-able interfaces that currently exist, and the reverse: allowing AppleScript to call DOSA interfaces.  VendingMachine is an application I am currently working on that will be based on plugins that it will load, each plugin containing a class that reveals a DOSA interface (and is vended) and the class launches AppleScript-s.  This way there'll be a vended object for com.apple.iTunes, com.apple.Mail etc.  The reverse, AppleScript-s calling DOSA interfaces will be implemented in stages: DoIt, VendingMachine will be made applescriptable (allowing connections to objects not vended in VendingMachine as well), and then a stand-alone OSAX.  This page was began with the hope of recruiting some people for these tasks, plus some periphery things; as well as just discussing the idea.  By the end of the month I should release code/downloads for some applications, example scripts, VendingMachine, DoIt, etc. at my website... (I have been using DOSA for myself for a while so there is already some example apps etc ready to go).

----

I had some more thoughts about this thing for documenting the interface.  Use HeaderDoc to describe the protocol and put the html file generated at DOSAProtocol/Protocols/BundleIdScripting/CompositePage.html in Contents/Resources/DOSAInfo.html.  The @protocol's @discussion tag should mention the bundle id that is being used to vend the object. Include the header with the protocol and headerdoc comments in Contents/Resources/DOSAProtocol.h... creating this file and copying them can be set up in XCode... This way there is (assuming you take the time to write good HeaderDoc) the document describing the usage of the interface as well... Come to think of it, does the Vanilla OSX (without developer tools) include headerdoc2html... if it does there's no need to generate the html, but have a program that just generates it as needed to display the scripting interface... This should be part of VendingMachine.

----

Just a word of support.  This sounds like a damn good idea to me.  Anything to get scripting on OS X without subjecting programmers to AppleScript hell will be a big improvement.

----

I agree... and ironically the place I could really use some help now is figuring out how to make an OSAX or something to give applescript access to DO objects... I did complete DoIt, so it is possible to call a DOScriptingArchitecture method with "do shell script" but it would be nicer if an applescripter could just write something like *tell distributed object "mac.mooresan.WikiPad" to do message "textForWikiKey:" with parameter "recent"* (currently *do shell script "DoIt mac.mooresan.WikiPad textForWikiKey:'recent'"* which admittedly is fewer key strokes, but it's one more layer of redirection...and the result returned is an xml plist which you have to parse...)

