---
layout: page
title: PrecompiledHeaderIsInvalid
---

I've been working on an app on my PowerBook GeeFour for a while now and it builds and runs fine.

This morning I tried to copy the project onto my  work machine, a GeeFour tower.  When I try to build it I  get a cascade of errors (2128 total), begining with:

"Precompiled header is invalid; header file /System/Library/Frameworks/Cocoa.framework/Headers/Cocoa.h is now different."

Both machines are running OS X 10.2.6.  What do I need to do?

----

When I try to compile my project, I get exactly 2128 errors. And you can see a screenshot here :

http://bwass.multimania.com/divers/lotsoferrors.pdf  (invalid link)

I don't know what to do. The only advice I don't want to read is : "Buy Panther". Remember that I'm poor and can't afford it... yet...

-- Trax

----

**Delete the build folder and recompile.**

----

I'd just like to add my heartfelt thanks for the suggestion above. After working on a project for some days, I found suddenly that I could no longer build it- the compile would hang up half way through, with whirling coloured cursor. The only way out of this was to force-quit Project Builder. 

I deleted the build folder as recommended above, and that fixed the problem. Thank you!

Ronald Cox

