---
layout: page
title: GarbageCollection
---

Garbage collection (hereinafter GC) refers to memory management schemes that relieve the user from having to explicitly free memory in their programs. There are several kinds of GC, and pros and cons to them all. Prior to Leopard, NSObject, from which all Cocoa objects are derived, uses retain-release memory management (object refcounting), which is not a form of *automatic* garbage collection.  Starting with 10.5, the developer can enable automatic generational garbage collection, but still use standard retain-release MemoryManagement techniques to maintain source compatibility when targeting versions of MacOSX prior to 10.5.

The core problem every GC scheme tries to solve is how to identify which objects are still live (will be needed in the future) and which are dead (the memory can be safely released because no one references it anymore).
Please note that this page presents a simplified overview of GC and alternatives to GC, but doesn't delve into the intricacies of basic heap management, which is a topic still germane to GC programmers. See the reference section  for more on the subject of memory management in general and the measured cost of GC in particular.

----**Simple GC: retain-release**----

With retain-release, every live object contains a retain count - a count of how many pieces of code are still using that object. When an object is first created, it has a retain count of 1. When a piece of code receives an object that it needs to keep around (usually, this means storing it in an instance variable), it increments the count; when it no longer needs the object, it decrements it. When the retain count reaches zero, the object disposes of itself.

Refcounting's primary claim to fame is that it's threadsafe, if properly implemented. Any thread can access an object without synchronization, because all threads that access the object first retain a reference to it. See RetainingThreadSafety for more on this. Unfortunately, retains/releases are SynchronizationPoints, and hence a drag on scalability.

Another benefit of refcounting is that it makes the timing and order of finalization deterministic. In most languages and systems, finalizers are generally not suitable for cleanup. However, in a few languages--most notably C++ (where "Resource Acquisition Is Initialization")--most cleanup takes place in finalizers, so it's very important that they be deterministic.

An intrinsic problem with refcounting is that it's easy to create circular references. Say Object A retains Object B, which retains Object A. If you release Object A, nothing happens because it is still retained by Object B, and you have a memory leak. With other GC schemes, you don't have to worry about this, because the collector knows how to deal with circular references.

----**Automatic refcounting**----

Some languages implement automatic refcounting, in which the interpreter/compiler adds the retain and release statements so that the programmer doesn't have to worry about it. For example, Python automatically refcounts all objects, while C++ has a shared_ptr that lets you automatically refcount only the objects you want.

By itself, automatic refcounting is a degenerate form of automatic garbage collection. As with manual retain-release, it can't handle circular references, which is a problem because circular references are more likely to occur in code where the programmer has no control over the retain count. The programmer must be careful to either avoid cycles (e.g., by using weak references, such as the C++ weak_ptr, in one direction) or to break them (e.g., by manually scanning gc.garbage in Python).

Some languages provide automated cycle breaking, which turns automatic refcounting into complete garbage collection--but, as with any complete garbage collection system, this makes the order of finalizers nondeterministic. In Python, cycles are broken automatically unless the objects have non-standard finalizers. In C++, cycles are never broken automatically.

----**Mark-and-sweep stop-the-world GC**----

Mark-and-sweep GC traditionally takes a stop-the-world approach to GC. As long as there is free memory available, objects are allocated as usual, although they are never released. When free memory runs out, the GC kicks in and stops all running processes ("stops the world"). It then follows all object references, guessing which objects are still live by whether they are referenced by other live objects (any object used in running code is assumed live). Any objects not referenced thus are considered dead and are released. Once this is done, the memory thus freed is used to allocate objects again.

The benefit of mark-and-sweep is traditionally given as ease-of-use for the programmer. In fact, the stop-the-world approach is also beneficial because very few SynchronizationPoints are needed to get it working, which may actually speed programs up despite the cost of the mark-and-sweep, especially if lots of objects are being allocated for very short timespans. Other schemes that do not stop the world probably lose this advantage.

The big cost is to latency - the ability of a program to respond near-instantaneously to real-time situations. For many programs, this is not an issue - nanosecond latency is simply not needed. Other problem domains cannot use mark-and-sweep for this reason, for example music (eg MIDI) programs. Low latency is impossible because during the mark-and-sweep process, the program cannot respond to external stimulus, so latency cannot be lower than the length of a mark-and-sweep.

Another cost is that the program will need to allocate more memory from the system, than it is actually using, because unnused objects will remain allocated on the heap until the GC kicks in.

Yet another disadvantage is that the "stop the world" may apply to more than just one program, if the program runs in an environment where objects can be shared among several tasks.

The other problem with mark-and-sweep is that unless the language you use was explicitly designed for it, it is imprecise at best. Storing two pointers bitwise-ORed together, for instance, is totally opaque to a mark-and-sweep engine because it no longer looks like a valid pointer. Be wary of any claims otherwise.

Java, C# and SmallTalk are examples of languages with various mark-and-sweep GC schemes (or at least schemes which look like mark-and-sweep to the programmer). Also, LibFoundation has some form of it built-in, demonstrating that while Cocoa does not have GC, ObjC is capable of it.

See also 

----**Compacting/Generational GC**----

More sophisticated mark-and-sweep-based GC schemes perform copying -- moving objects about in memory without disrupting running code - one simply changes all pointers to that object when one moves it. This is easier in some languages (Java, Ruby) than in others (C, C++, ObjC).

In generational GC, copying is used to keep long-lived objects in separate memory areas from short-lived ones. This has the benefit of potentially speeding up the mark-and-sweep stages of GC, because one need only sweep dead short-lived objects and move live ones to the area for longer-lived objects.

In compacting GC, copying is used to combine longer-lived objects into clumps, but without setting aside special memory areas for this purpose. This has the benefit of eliminating memory fragmentation. Some generational GC's also compact.

----**Memory fragmentation, and Why it is Evil**----

Memory fragmentation is when live objects are scattered throughout free memory, and usually occurs when long-lived and short-lived objects are intermingled on allocation without regard, usually as a result of naive coding, though sometimes unavoidable.

Memory fragmentation is evil because it causes your program's working set size (the number of virtual memory pages that your program touches in normal usage) to grow beyond the size your program really needs. This ultimately results in more system-wide paging and disk thrashing as the VM system moves pages to and from RAM, a bad thing for all involved. See Apple's Performance book for more on the subject, and for tools to measure and optimize your working set.

Allocating objects from fragmented memory also means many SynchronizationPoints in your code, because it neatly defeats most schemes for avoiding such synchronization. These problems do not affect the kind of simple memory allocation scheme that Mac OS X uses, because all memory allocations are typically synchronised anyway.

The standard memory allocation library on Mac OS X minimizes fragmentation by grouping similarly-sized objects into different pools of memory. This is a standard heap management technique. While you may find cases in which manually optimizing your memory usage will perform better, the standard behavior is fine for most programs. Note that your attempts to minimize fragmentation can easily do a worse job than this optimization. As always, be sure to measure your performance and memory usage before and after to be sure that you're actually making improvements and not de-optimizing your program. This applies to usage of NSZone as well: Apple has found that NSZone is usually more trouble than it's worth. (In fact, in 10.0 and 10.1, Cocoa didn't even implement multiple zones: regardless of the NSZone specified to allocWithZone:, all memory was allocated in the default malloc zone. This was fixed in 10.2, but it didn't result in a speed up.)

Although any memory management scheme may attempt to minimize fragmentation, some GC schemes can entirely avoid fragmentation. Generational GC avoids it because at every sweep, all the new live objects are moved out of the short-lived memory area, leaving it empty and totally unfragmented. Compacting GC avoids it more explicitly by congealing live objects together, removing the fragmentation explicitly.

Compacting GC can sometimes be more efficient that generational GC. With some generational collectors, **every** sweep involves moving objects. Compacting GC needs only be applied when necessary, allowing more intelligent schemes. Secondly, one need not move **every** new object, so intelligent object allocation by the programmer can be rewarded by improved execution times.

----

There is an entire book on GarbageCollection: 
 
Garbage Collection: Algorithms for Automatic Dynamic Memory Management
by Richard Jones and Rafael D Lins,  List Price:	$85.00 



Lots of detail!
http://tonyguards.edublogs.org Please Visit | http://blog.unsri.ac.id/justgerrardz Please Visit | http://adrian-fh98.web.unair.ac.id/ Please Visit
----

See also GarbageCollectionDiscussion.

----
*Discussion*

Now that GC is a reality in Leopard, I think this page could use some serious refactoring. Maybe the current theoretical stuff could be moved to GarbageCollectionTheory and this page would then be used for practical notes on Leopard's ObjectiveC GarbageCollection. While it's simple in many cases there are a lot of details that you need to know for more complicated cases and I think that having a page dedicated just to Leopard's implementation, how it works, and what you as the programmer need to know would be helpful. Any thoughts? -- MikeAsh

For what it's worth the .NET platform (and by extension, C#) uses a Generational GC, at least on Windows (not mark and sweep). Mono's implementation may be different.
 Pour participer   maintiennent numéro, vous aurez  pu   compte   Agent  ( signal ) [http://obtenir-rio.info numero rio]. Vous obtiendrez  êtes certain d'obtenir  pour  aucun coût par  téléphonant   du serveur ou du service à la clientèle  support clients   du  vieille fournisseur  [http://obtenir-rio.info/rio-bouygues numero rio bouygues] . Vous ne  CAN  recevez immédiatement  un SMS  avec vos . Avec  votre propre  [http://obtenir-rio.info/rio-orange code rio orange], alors  il est possible d'  vers le  offre de  de votre choix respectifs  en orange orange .

