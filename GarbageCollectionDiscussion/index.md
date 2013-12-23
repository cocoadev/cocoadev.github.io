---
layout: page
title: GarbageCollectionDiscussion
---

The problem with ANY auto-gbc is the performance hit. No matter how fast you run auto-gbc there will be a lag on it with lots of objects. C#'s algorithm is no exception (generation reference counting isn't exactly brain surgery btw). Until someone commits to making a real GC scheme that can be turned on and off, Apple should avoid making a cruddy system. I'd rather see Apple finish everything else and tackle the gc problem with a lot of engineers as a main project. Autorelease works plenty well for right now, it's a nice tradeoff between ease of use and performance. -- DaveFayram

----

Those bashing Java's garbage collection would get a lot more respect from me if I thought they actually knew anything about it. Why not do some reading?

http://java.sun.com/products/hotspot/whitepaper.html.

At least then you would know what you're bashing. Don't let the relative primitiveness of Java 1.1's gabage collector fool you. -- AndyT

----

I would like to note that adding GC to ObjC is **not** trivial.
Proper GC, i.e. one which does not just mark-and-sweep dead objects, but compacts memory or uses some generational model, must be able to positively identify **all** object references, because it will need to modify them during a sweep. Here's why: proper GC moves objects around to alleviate memory fragmentation, which has terrible costs not just for allocating large objects (when one needs to find a large contiguous block) but because memory fragmentation totally screws up multiprocessor efficiencies. ObjC is not designed for such things; Java and C# are. I'd love to go with adding GC to Cocoa, but I can't say I believe such a thing is possible. -- KritTer

I don't think it's possible because C is quite frankly not safe enough. If I stick a pointer in a union, which I am allowed to do in ObjC, or even if I cast a pointer to an integer, I've immediately rendered proper GC impossible, because there is no longer any way of knowing whether that union (or that integer) refers to a memory location or something else. It's not a matter of "growing" ObjC; the language needs to be designed to support such things. Apple would have to think long and hard before making language changes that would most likely invalidate decades of old code. -- KritTer

----

Fair enough... but then, the GC could have the known limitation that it would ignore unions, or something like that. You're probably right about this, but I still think that it should be possible to add *something*.
On an aside, while LibFoundation's GC may not be "proper," i.e. it just marks-and-sweeps dead objects, isn't that better than what we've got now? What disadvantages does that have over "proper" GC? What disadvantages over retain/release? Can we graft it onto Cocoa, or if not, what would it take to add it to our own object hierarchies (for those of us who don't always equate Cocoa and ObjC)? -- RobRix

----

That would be a crazy limitation, and the fact that working code casts pointers to and from integers (which would die very messily if the code was then interrupted for a GC) rends it unworkable anyway. What disadvantage over compacting ("proper") GC? As I said, clearing memory fragmentation is possibly the most significant speed benefit GC can provide, especially if properly implemented. Compacting GC gives this benefit; mark-and-sweep GC does not. What disadvantage over retain/release? GC is expensive. True, so is the synchronisation involved in retain/release, but quite frankly I doubt it's as excessive, unless you regularly send objects thousands of retain/releases (in which case, stop it!). In actual fact, without examining Apple's code, it may well be that retain/release is not by default thread-safe anyway. Hence GC will be quite expensive, and provide no speed improvements to offset that, unlike a compacting GC. What would it take to add it to our own hierarchies? We would need to be able to inspect all currently-running code for object pointers. This is not possible without the language explicitly supporting it. -- KritTer

----

I didn't mean adding compacting GC to our own hierarchies. I just wondered if anyone knew how LibFoundation did it. As for fragmentation, isn't that what NSZone is there for? +allocWithZone: instead of +alloc, and you can control fragmentation (albeit brutally manually). As for speed... NSAutoReleasePool is a form of GC, is it not? Is that why it's slow, because you don't see any speed advantage from compaction? Also... if GC is something that would be beneficial, maybe it's worth a major (i.e. not entirely backwards-compatible) refactoring. Obviously, this isn't necessarily a project that Apple would have any interest in taking on, but it's perhaps something we (the people :) ) could take on. Pipe dream (it's what I'm famous for), but if there's enough interest, why not? -- RobRix

***NSZone*** It's possible, but not the easiest thing in the world. Perhaps if there was documentation about how to plan things like that (which we at CocoaDev could do) and tools to plot fragmentation and the like (which would take much more effort, but would be vital), then we could begin using the full NSZone potential. I would be willing to help some on this front, as I have a small amount of experience in that area now, though not for Cocoa. The nice thing about compacting GC is it does all this nonsense for you with only a small overhead cost. ***Autorelease*** Actually, retain/release is itself a form of GC, technically. That aside, autorelease indeed provides no compaction benefits. ***Altering ObjC*** You would need to: *Write your own compiler, because C doesn't support checking of runtime variables from other threads *Learn how decent GC works, including dependency mapping, compaction, efficient heap strategies, etc *Probably recompile the entire Darwin ObjC libraries Be my guest ;) -- KritTer

An update: I've added a list of gripes to the NSZone page. They severely limit our hopes of making a more optimal memory-allocation scheme with NSZone, but I'm sure we can work around any real problems. -- KritTer

**NSZone** - I admittedly know very little about NSZone or any of that stuff, but I'd read on a list that it could be used to help avoid fragmentation. I'm now reading up on it quite a bit and I'll see what I can work out in my own code :) **Altering ObjC** I have no fear of writing a compiler whatsoever. It's not an if, it's a when, with me (one for which you are partly responsible; I learned about asm cos of you!). Also, it might be possible to create an ObjC-interoperable language (can anyone say JavaBridge?); ideally, this would let you use Cocoa without having to use Cocoa's memory management (whether by "smart wrappers" or some other means, I know not).

But that's not first on my plate. First on my plate is getting this game I'm working on to a playable state. And doing heavy thinking about ArtificialIntelligence (research, not game). And reading. And writing. And hey, there's a guitar in my bedroom! :) -- RobRix

Help avoid, yes. But it's far from ideal. As to your writing a compiler, good luck to you; I'll be happy to advise about GC while you write it. Incidentally, if this conversation is stale for more than a week I strongly suggest one of us refactor it, probably to another page (GarbageCollection?), because it's not easy to follow right now. -- KritTer

----

Have those of you 'vetoing' garbage collection actually spent any time debugging serious Cocoa applications, or in fact spent any time using garbage collectors with real languages (not Java)? Show of hands, please. Now, have you run MallocDebug over your application to isolate memory leaks? Find any? In a large Cocoa app, you do spend time debugging crashes related to retain/release or memory leaks also related to retain/release. And any new code you add just exacerbates the problem.

The retain-release/NSAutoreleasePool paradigm is necessary for multithreading; it is _not_ a substitute for real garbage collection, whether it be generational or mark and sweep or copying or some amalgam of all three.

I'm amazed at some of the GC myths (and lack of understanding of basic computer architecture!) in this discussion. Casting a pointer to an integer doesn't matter -- with C/C++ garbage collectors, GC is looking at the raw bits in the heap, not the types of those bits.... GC can in many cases be more efficient than manual memory management, especially with a generational collector. See the "Related information" at http://www.hpl.hp.com/personal/Hans_Boehm/gc/.

Where GC falls down is when you do silly things that disguise pointers. By 'disguise,' I mean manipulating the bit value to make it into something else, like storing the XOR of it with some other value (used by some overengineered doubly-linked list implementations).

If you call retain/release a "form of GC", you have to call malloc/free a "form of GC" as well. They are, in fact, garbage collection -- MANUAL, error-prone garbage collection. Really, they're not what anyone means when they talk about GC.

And as for threadsafety, that's what retain/release is all about. You have to place the retains in the correct places to do that, but if you're following the guidelines, you'll do that automatically.
----

Any garbage collector that wants to move objects around has to make sure you don't cast pointers to integers. Think about it. And if you're paying the cost of suspending the whole program intermittently to do mark-and-sweep, you might as well make use of it and do compaction.
----

It's not just us who associate retain/release with garbage collection. It **is%** a form of garbage collection, so what's wrong with stating that? Malloc/free, OTOH, is not. I didn't invent the nomenclature.

Regarding your refactoring of GarbageCollection: zoning doesn't work largely because Apple never bothered trying to implement it well, at least IMO. You might have gathered that from my comments at the end of NSZone, though.

-- KritTer

----

Retain/release is simply not GC. When people talk about GC, they're talking about automatic garbage collection. When you use retain-release (the way it's used in Cocoa, i.e., refcounting), it's not garbage collection. Retain-release is a simple-minded GC iff it's handled by the compiler, as it is in Python. Refcounting is not GC.

Also, AllanBaruz, can you be more elaborate on exactly why you think casting pointers to integers interferes with GC? I don't understand your objection.

*No, that's me. Let's suppose you're moving objects around, and code can cast a pointer to an integer and back. That means that whenever you see an integer with the value of the address of the object you just moved, you have to change its value. Except it could have taken that value by coincidence, in which case you've just stopped your code working. Whoops. -- KritTer*

----

Please people stay on technical grounds. First of all: Automatic Garbage Collection with Obj-C can be done and has been done. Just look at http://gnustep.org/ . Also its totally obvious that if you do Garbage Collection with any C-Based Language then you need a _Conservative_ Garbage Collector (such as the Bhm/Demer/Weiser one at http://www.hpl.hp.com/personal/Hans_Boehm/gc/ )

So now that we have laid out the ground lets talk some sense here:
- Remember the first rule of optimisation: DON'T
- 99 % of all programmers will never have a speed impact because of Automatic GC
- 90 % of all programmers will probably see a speedup because the less copying is occuring in the program
- If you don't profile your code and prove me that the automatic GC is the source of it's slowness I am not going to believe you and more importantly YOU are wasting your time optimising something that is _not at all_ the reason your particular program is slow.
- YOU can deactivate automatic GC as much as you like or just for single objects.

If it saves more time during development of new apps than it takes to implement it it is a sensible engineering tradeoff to do so and implement it.

-> Now this is the only question that needs to be answered. I don't believe that right now it would be the most rewarding thing for apple to implement, but it soon will be one of the biggest improvements that can be done to the Obj-C language and the cocoa framework. --MartinHaecker

I've long since been in favour of GC. However, the point was not that automatic GC couldn't be done with ObjC/Cocoa, but rather that the GC would necessarily be conservative--like you mentioned. Thereby losing the advantages of compacting GC with regard to fragmentation et al.

Now, I'd like to have a very good garbage collector. I'd like Apple to implement one, maybe based on the example you gave. But I don't think they will. Options? -- RobRix

Options:
- The Simple one: Use GnuStep
- The Complex one: go with these people: http://cocoa.mamasam.com/COCOADEV/2003/11/1/75999.php
--MartinHaecker

Thank you. For completeness, here is a link to an earlier e-mail discussing the possible use of conservative GC with Cocoa:

http://cocoa.mamasam.com/COCOADEV/2003/05/1/62933.php

++ on adding GC to Cocoa. I've done a lot of Java recently, the 1.4.x GC is getting very good, and tunable for your specific needs.  Aside from that, I've done Ruby, Perl, SmallTalk and other languages where it wasn't necessary to manage your own memory.  I've not done any .NET programming yet.  Yes there are still programming problems for which a garbage-collected language imposes resource usage problems, but for at least 80%, if not 95%, of the programming problems we as an industry work on today, programmer time is much more important than raw running time. Arguably the #1 reason to have some kind of GC is the problem of retain cycles. -- StevenNewton

*Indeed. For my own peace of mind, I will have to do a comparison between compacting and conservative GC with various languages.*


An interesting reference is:

http://home.pipeline.com/~hbaker1/CheneyMTA.html

wherein is described a system used in a Scheme-to-C compiler to basically garbage collect the stack... it's esoteric and fantastic and weird, but I wonder if some of its principles might be applicable.

----

Did anyone try to experiment with the GC support already present in GCC?

Quoting from file:///Developer/Documentation/DeveloperTools/gcc-3.3/gcc/Garbage-Collection.html

*Support for a new memory management policy has been added by using a powerful conservative garbage collector, known as the Boehm-Demers-Weiser conservative garbage collector. It is available from http://www.hpl.hp.com/personal/Hans_Boehm/gc/
*

*To enable the support for it you have to configure the compiler using an additional argument, --enable-objc-gc. You need to have garbage collector installed before building the compiler. This will build an additional runtime library which has several enhancements to support the garbage collector. The new library has a new name, libobjc_gc.a to not conflict with the non-garbage-collected library.*

*When the garbage collector is used, the objects are allocated using the so-called typed memory allocation mechanism available in the Boehm-Demers-Weiser collector. [...]*

This doesn't work for the NeXT runtime.

----

Does GC play well with Cocoa? DustinVoss

----

To the person who said that C# is not a standard, it is, actually, and an open one at that. As far as GC goes, I can't decide. I use .NET and C# at work, and not having to worry about memory management is very nice (although I prefer ObjC in all other aspects, I find C# to be a bit ugly). I've never noticed any performance hits that could be down to C#'s GC, but our apps don't tend to create or destroy many objects, and we certainly don't need to do any time-critical stuff. 

At home, I use ObjC for a much wider range of tasks, where performance is very important, and I'm concerned that automatic GC might screw some of this stuff up. Also, wouldn't it require that Apple re-wrote all of the ObjC part of Cocoa, and for everything that uses Cocoa to be rewritten?

----

Making Cocoa work with garbage collection should be as simple as adding a category to NSObject that turns the default -retain, -release, -autorelease, and -dealloc implementations into NOPs, and making the GC's finalizer call -dealloc for cases when an object does something important, like close a file, when it's deallocated.

There will probably be complications, but that's the theoretical part of it taken care of. This would also have the advantage of allowing the use of Cocoa without GC, by simply not including this category.

----

I'm confused. At this years WWDC I clearly remember an Apple employee mentioning on stage that they are working on adding basic (at first) garbage collection support. Am I wrong?

Also, I should mention that GC is a great thing! I have used memory management techniques and I honestly think that memory management is just another repetitive (and critical) task that the computer should be handling for us, which is what computers are for, right? Even if it effects performance slightly... look, computers are much faster than they were 5 years ago, so let them handle a few extra things so we can deliver better applications in less time.

----
But the whole point of being able to do memory management is that I have control over what's going on in the program.  I don't want to wait until the end of the RunLoop for GC to happen.  I want to release this object NOW.  I guess, two different schools of thought.  I started learning with C way back when, and I guess it's one of my major gripes when I learned Java - no manual memory management.  When I learned ObjC I thought that reference counting was a pretty good idea, especially with autoreleasing.  So I'm mostly happy with the way things are right now.

VinayVenkatesh
----
You could have +alloc return an OS 9 style handle - a pointer into a block of master pointers, each of which points to an object.  Then you modify the obj-c code generator and runtime to handle the double-pointer.  You can use the block of master pointers to do OS 9 style heap compaction, on demand.  I guess you'd have to suspend your other threads while doing this...  --DavidVierra

*Of course, you can't compact the master pointers...which somewhat negates the point.*

**You do recognice that since they are all the same size, compaction isn't neccessary?**

The point of compaction that I was arguing for is that in multithreaded code (which is set to become more prevalent), you can avoid SynchronizationPoints if you can allocate large chunks of memory at once. If you're not worried about those, you don't really need compaction in the same way. If you are, having everything the same size doesn't help because there's still fragmentation! However, for the majority of applications, the scalability problems of memory allocation are really not going to be a problem, and indeed it was probably foolish of me in hindsight to bring it up, given the two issues have gotten so tangled in the discussion. So, one last time, here are the two points:


* Garbage collection saves the programmer a lot of inconvenience, at the cost of excluding certain problem domains (real-time, mainly). So does CoreData. So does ObjC. This is therefore a great reason to use GC in Cocoa.
* Garbage collection can *also* be used to **compact** memory in languages specifically designed around GC. This gives real, demonstrable performance improvements on multithreaded programs. It's also inappropriate for Cocoa, because it can't be done with a conservative collector (or, for that matter, handles).


I suspect we could usefully blow this whole page away. -- KritTer

----

Also, heap compaction isn't too useful with modern memory managers. --BenStiglitz

*Do you have any evidence to back this up? Right now, this page is simply a lot of unsubstantiated gossip :)*

I was hoping that this page might hint at when automatic GC is coming to Cocoa, but it seems to be a lot of nattering by people who don't actually have experience writing real applications and services in an environment (like .NET) where you have such a facility.  A year ago I too scoffed at automatic GC (being a C++ programmer, primarily in games)... but after a year of using C#/.NET I am a complete believer.  The time savings from simply not having to worry about (and debug) the problems of manual memory management are enormous -- productivity is much MUCH higher and bug counts are much much lower.  And some of the time I saved not worrying about it I spent in the profiler discovering where my performance problems REALLY lay, and fixing those.  Guess what?  None of them involved the GC.  C#/.NET certainly isn't perfect, and is missing all sorts of things (even though it is an overly huge framework), but automatic GC is one thing that MS most definitely did right.  Granted it would be nice to occasionally have precise control over the timing of object destruction, but 99% of the time IT DOESN'T MATTER so the system should deal with the case that happens most often and give you an escape hatch for the exceptional cases.  This leads to the C++/CLR stuff which looks really hot, despite increasing the complexity of an already overly complex language.  Apple has a great thing in Cocoa, but ObjC is lacking in a couple of basic areas, primairly automatic GC and the loose manual nature of the accessors.  These omissions could really hurt them going forward as developers get used to having them in .NET (well, GC anyhow... the .NET properties don't save you much but do have certain advantages).

----
We don't really need much evidence -- most of us know what's being implicitly  referred to.  (i.e. by "modern memory managers" I'm pretty sure off-board mach style managers is what's meant).

As a developer of scientific apps -- apps that are very, very computationally intense, and are frequently deployed in distributed computing environments -- I can unequivocally say that GC is not an option.  Cocoa has been great in significantly reducing production time, but we would have to drop our entire cocoa code base if suddenly memory management became fully automatic.  Even relatively small expenses like dynamic dispatch can really put the breaks on performance, but at least you can work around that...

*Is it possible to alter your applications' memory usage profiles? If you are allocating and releasing a lot of memory (the only reason GC would slow down your computations), then this may be a good target for optimisation already!*

I am a developer of signal processing software with soft-realtime requirements, and can relate to the hesitation of the developer of scientific apps. However I have found out that GC does NOT need to interfere with all parts of an application. Sorry, I have not read the entire page but I assume that the discussion concerns replacing OBJC retain/release system with GC and NOT malloc/free (which I am unequivocally against, hehe read the next part...). I recently tried the Boehm-Demers-Weiser garbage collector from HP (which replaces malloc/free) in a C++ project. The app area's with custom memory system required the least amount of changing and retained their realtime performance. True, that some more care is required in separating the time critical parts from the GC parts, but time critical code usually has its own memory manager anyways, and so already is segregated from other code. Cocoa is rather unsuited to realtime development, mostly because of autorelease pools, but also due to dynamic dispatch. So adding a GC would hardly be tragic on that front. Besides that, GC is really a step up for application programmers, (I assume most cocoa developers write apps and not drivers :) It has really sped up the development cycle for a lot of developers (mostly by shortening the debug part). After spending several months with GC, I find myself frustrated with the retain/release system that cocoa provides. But cocoa still beats the brains out of VB (and most other dev-systems!).

*Additionally GC in Cocoa would solve the current problem with retain cycles which are more frequent after the introduction of bindings, and often very hard to avoid.*

GC can also leak memory in complicated graphs. There has to be a well interfaced NSGarbageCollector class with manual flush methods so that long running programs do not kill the system.

*The above is totally false.  If there is no pointer (direct or indirect) from anywhere in the stack, it doesn't belong in the heap.  That is the definition of a leak and is a way to do GC.  Moreover, while retain cycles can be annoying, a well designed program need never have any retain cycles*

**Which is probably why the poster stated "complex graphs". You still need to remember to remove all references (it's a memory leak if something's never *used* again, not if it's not *referenced*), and in a complex graph that is a problem - just as remembering to retain/release is a problem, only less so.**

It is not totally false with **Conservative GC**, which I assume cocoa would use, to retain compatablility with C and C++.

----

Conservative GC can 'leak' memory if it comes across a value which points to an object, but isn't actually treated as a pointer. But this has nothing to do with the complexity of the object graph.

On another note, nobody on this page seems to have noticed that Tiger brings optional support for garbage collection in Cocoa. It's detailed in the release notes.

As far as I can see it's not yet finished and won't work on 10.4 (sadly). At least thats what the decoumentation here tells me: http://developer.apple.com/documentation/Cocoa/Reference/Foundation/ObjC_classic/Classes/NSObject.html#//apple_ref/doc/uid/20000050-BBCEBIAG --MartinHaecker

*I think the GC support was mentioned in the release notes in a Tiger seed, but it seems to have completely disappeared in the final release. Sounds like it was very nearly ready, but not quite. Hopefully it will show up in a Tiger revision. I guess I slightly, and accidentally, violated my NDA above, oops.*

----
I think that this link could be interesting : http://lists.gnu.org/archive/html/discuss-gnustep/2005-04/msg00184.html . If I understand well, one Apple developper has commited patchs to gcc in order to have GC. BenoitAstruc.

----

some interesting tidbits here: http://lists.gnu.org/archive/html/discuss-gnustep/2005-04/msg00186.html

it also looks like GNUstep is getting ObjC++

*
"Apple is working on a garbage collection system for Objective-C. It is still under development, but does not use the Boehm GC available in GNUstep. These compiler changes mostly add a write barrier when assigning Objective-C instance pointers to ivars and a few other places. The write barrier is a runtime function, and is similar to the fast dispatching above. This is likely to evolve further, and probably isn't interesting for GNUstep yet."
*

