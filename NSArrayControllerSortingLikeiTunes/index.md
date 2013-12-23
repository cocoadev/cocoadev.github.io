---
layout: page
title: NSArrayControllerSortingLikeiTunes
---

I've made a small app to sort out song lyrics, using CoreData and NSArrayController. iTunes has the nice feature of ignoring articles such as "The" or "A" when it sorts the songs by titles. Is there an easy way to do that with an NSSortDescriptor or something applied to the NSArrayController?

----

If you already know about NSSortDescriptor, you're part way there. Read the docs, create a subclass, and write your own -compareObject:toObject: method ... The rest is more of a "how to sort strings alphabetically with exceptions" question.

