---
layout: page
title: OpenStep
---



OpenStep is the set of libraries that eventually became the YellowBox in AppleRhapsody, and finally, Cocoa.

A cadre of dedicated users have implemented an open source version of the OpenStep standard known as GNUStep.

OpenStep was also an operating system for a few different architectures, sold by NeXT. In time, it became AppleRhapsody, and eventually MacOSX. OpenStep was sometimes thought of as NeXTSTEP 4.

----

Not to nit-pick, but I thought the OS was called OPENSTEP, not OpenStep.

Also I don't see much similarity between Mac OS X and OPENSTEP. The only thing they have in common is the higher level OpenStep API aka Cocoa and NetInfo, everything else is different. The driver architecture is different, the low level APIs are different, the display server is different, the kernel has been totally rewritten, etc. If the only similarity is one API then Mac OS X doesn't derive it's lineage to any greater degree from OPENSTEP than MacOS.

The most important aspect, in my opinion, was derived from MacOS: the human interface guidelines.

-- PaulBayley

----

Well,  I would call that nit-picking  ;)  but you're correct anyway;  I have an OpenStep 4.0 box on a shelf here, and it is indeed labelled OPENSTEP on the outside as well as in the manuals, fwiw.  I think that the Objective-C development API was called OpenStep however.  Anyway, most people just write OpenStep (maybe we don't want it to look like we're SHOUTING).

As for the lineage from NeXTSTEP to OpenStep to Mac OS X, let me just say that if you had used NeXTSTEP or OpenStep extensively, you would certainly "feel" the relationship with Mac OS X.  You're right that huge chunks of the guts have been ripped out and replaced, but to a large extent they've been replaced with upgraded versions of the same parts.  The low-level APIs are actually about the same; the BSD kernal was "just" upgraded to a newer version;  The display server is actually quite similar to DPS in terms of capabilities and even, in many cases, APIs (some have likened it to a re-write and extension of DPS, minus the PostScript language interpreter).

-- JackNutting

----

NeXT had some real strange naming for it.
Here's the ObjectFarm info on OPENSTEP/OpenStep:

OpenStep

... was born as part of the deal with Sun. OpenStep stands for an API specifications which was based on an evolution of the existing NEXTSTEP APIs. Most of the new technology already was available inside NeXT's labs but needed a renewed system to get integrated. OpenStep does not refer to any "real" implementation. It stands for the abstract API definitions which a vendor has to support in order to call his system "OpenStep compliant". OpenStep consists of the AppKit, FoundationKit and the Display PostScript layer.

OPENSTEP

... is NeXT's specific implementation of the OpenStep specification, and basically represents NeXTSTEP 4.0. This was yet another marketing move to get rid of the old name. OPENSTEP is a dual personality which still provides the NeXTSTEP compatibility libraries to run applications which are based on the old APIs. Additionally it has the new OpenStep compliant frameworks. The look & feel remained the same, and so users hardly can tell the difference between both worlds.
The full naming is OPENSTEP for MachOS/[NeXT Computers, Intel, SPARC].

there's also more.. OPENSTEP for Windows, and OpenStep for Solaris... confused? :)

http://www.objectfarm.org/Activities/Publications/TheMerger/OpenstepConfusion.html

-- Anonymous OpenStep user :)

