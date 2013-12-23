---
layout: page
title: NSMethodSignatureRetainCount
---

Does anyone know how NSMethodSignature looks after it's own retain count? 
Releasing/retaining it appears to have no effect, it always seems to be -1.
I am guessing it just ignores release|retain and just keeps it at -1.  Or is something more complicated going on?

Sorry, I should say the retain count is always 0xFFFFFFFF (max value of an unsigned int), not -1.

----

You hit the nail on the head, a value of -1 (sorry, I even think of it like that) tells the runtime that it should never be deallocated, usually because it was loaded in place as part of your executable's DATA area.  An NSMethodSignature is generated at compile-time for each unique method signature in your application in the same way that a Class object is generated for each unique class and an NSString object (or rather an NSConstantString) for each unique @""-style string in your app. -- Bo

----

Cheers, that answers the question. So is the runtime data structure of NSMethodSignatures accessible? + signatureWithObjCTypes: must be looking somewhere for NSMethodSignatures.

