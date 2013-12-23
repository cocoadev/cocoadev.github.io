---
layout: page
title: AtomicThreadSafety
---

AtomicThreadSafety is something which you should not be concerened with in Cocoa.
Use mutex's and semaphores, don't bother with using atomic (lock-free) techniques, as they are VERY hard, little understood and usually unneeded. You might want to make use of them in a time-constrained process, but Cocoa is unsuitable for that anyways, because of it's unpredictable memory behavior. Alternatively, what  (relatively) little research has been done indicates that atomic techniques seem to scale much better than lock based techniques, so if you are writing a server dealing with a potentially large amount of processors, it might be worth your while to investigate. Most portable atomic techniques use the compare and swap - like functions available on modern PC processors and embedded processors alike. You might be able to get away with more processor specific routines, or assembly language if you know your target platform, but this will make your code HIGHLY unportable. This is worse than it sounds because some very cool atomic techniques rely on operations that many processors simply cannot support (like DCAS, double compare and swap), and thus you cannot port your code at all, as the very architecture may depend on these operations. --JeremyJurksztowicz

----

Atomic multi-word compare-and-swap can be implemented on any architecture that supports an atomic single-word compare-and-swap operation.  I don't have the paper with me with which to look up one of the many papers on the topic, but I bet an appropriate Google search would turn them up.  Also, it is incorrect to equate *atomic operations* with *lock-free data structures*.  The former are critical to implementing the latter; however, atomic operations have many more uses than just implementing lock-free data structures.  For example, atomic operations are also critical to the implementation of locks.  -- ChrisHanson

----

True, I fudged the definition, but most people I have been talking to refer to atomic operations when they want to avoid locks, sloppy language, my mistake. Multiple *adjacent* words can be swapped atomically on some architectures (usually only 2, ie 64 bits), but true DCAS is not available. If you can atomically swap multiple words using only CAS, then this is a new development and a huge one, with important consequences for lock-free design. As far as I know, very few processors support DCAS. Wikipedia seems to confirm this, but I am not an expert. I would be very interested to hear of any research. --JeremyJurksztowicz

----

Paper 1: http://citeseer.ist.psu.edu/705549.html
Paper 2: http://citeseer.ist.psu.edu/harris02practical.html

The algorithms in question look shockingly complex, but they do implement multi-word CAS using just single-word primitives. They obviously won't look atomic to arbitrary code, since the individual words still have to get stored one by one. At least one of the paper provides an atomic read operation to go with the atomic write operation, and I assume that would be standard procedure.

Having DCAS would have come in handy for me a while ago after I implemented a non-locking linked list and stupidly made it a doubly-linked list. Rare random failures on multi-CPU machines followed, and then I realized I had an inherent design flaw rather than just a bug to fix. Oops. Reimplimenting it as a singly-linked list solved that problem, but lost me some useful properties.

This sort of thing could come in handy as a library, for writing data structures for CoreAudio callbacks and the like, but somehow I'm doubtful such a thing will show up. If I personally wrote such a thing, I'd be worried enough about its reliability in my own code, let alone other people's. -- MikeAsh

