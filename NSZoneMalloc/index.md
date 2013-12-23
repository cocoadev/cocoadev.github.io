---
layout: page
title: NSZoneMalloc
---

Any reason to use this over the standard malloc?

----

NSZoneMalloc() allows you to specify the zone to use for allocation; malloc() has to find an appropriate zone. I believe both call through to malloc_zone_malloc() on Mac OS X. Other than that, no, not really. Compare +alloc versus +allocWithZone:.

----

As far as I can remember using allocWithZone: prevents you from using auto garbage collector in Leopard.

----

Really? On Tiger and earlier +alloc just calls through to +allocWithZone: with the default zone. Do you have a source for this? I'd like to read more.

----

You're right about alloc calling allocWithZone: with the default zone. However I may have to double-check this again, but garbage collector requires you to use the default zone. Also returning nil in initiliazers is a bad idea (if you check Apple's code, they use it quit often) for garbage collector. According to Leopard documentation you must always return self.

----

Sounds like a badly-designed garbage collector! How might one fail unexceptionally in these situations? How might one return an object different from the receiver (as, what, half the classes in AppKit and FoundationKit do?)

----
Is this documentation that claims you must always return self public? If so, I would like a reference so I can verify this claim. If not, then you're violating the NDA and this discussion should be placed on hold until after Leopard ships.

