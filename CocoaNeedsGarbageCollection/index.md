---
layout: page
title: CocoaNeedsGarbageCollection
---

Because even with reference counting, retain cycles are unavoidable, in certain situations. For those situations, the memory usage is unbounded, which leads, eventually, to a crash. So if you don't want your app to crash, ever, you need a garbage collector. OK, so retain counts are fast and they usually work. That is not good enough. Memory management should *always* work.

Look at what a garbage collector offers: you won't need to think about memory management again. Look at what it did for Java.

Mike Amy

Links:

http://www.hpl.hp.com/personal/Hans_Boehm/gc/gcdescr.html

----

As long as the user is allowed to use pointer arithmetic, good garbage collection is impossible.  And I don't think there's such thing as an "unavoidable" reference cycle.

The problem is that it is basically impossible to do well in a language that includes C as a subset.

----

Maybe they should create a subclass of NSAutoreleasePool (or a similar class) that garbage collects during its lifetime in a seperate thread or something... -JediKnil

GarbageCollector would be a subclass of NSAutoreleasePool. 
Use class posing in the main function to change the whole memory management system. Autorelease then transfers responsibility to the garbage collector so autoreleased objects are forcibly deallocated when the garbage collector realises nothing points to them. Distributed objects can still be managed using ordinary retain-release. This would be a hybrid system that would require few or no changes to user code. It can also be faster then boehm since Objective-C is much more cooperative. The garbage collector could still run at the end of the event loop when release is called on it, which would be great for interactive applications. 

Actually the Mach kernel is very helpful when it comes to garbage collection. It is almost a cinch to get threads, registers, stacks etc. The BDW gc has a lot of workarounds in other systems.

I have written a fair bit of such an implementation, with a plugin gc algorithm interface. Will post this implementation soon on MikeAsh's site so you can all have a laugh at my mistakes -- MikeAmy

----

*as long as the user is allowed to use pointer arithmetic, good garbage collection is impossible*

Disallow pointer arithmetic for id-references, problem solved. --TheoHultberg/Iconara

----

Most objects are pointed to by a specific type (e.g.     NSArray *), so that wouldn't work. You can't disallow pointer arithmetic for pointers and still remain a true superset of C. It has to retain all of C's rules for the types that exist in C, such as pointers.

*Nope, ObjC has no types,     NSArray * is just syntax sugar. It's all id-references once the typechecker has done its job. By "disallow pointer arithmetic for id-types" I mean that if you state clearly that if you try to do pointer magick on id-references and use this GC, you're screwed. You're probably screwed anyway if you do funny stuff with id-references.* --TheoHultberg/Iconara

----

Try using a scripting language that has Cocoa bindings, such as Perl, Python, Ruby, or IoLanguage (or even AppleScript!). These languages tend to be just about perfect for controller-type code, and although that seems to be on the way out with e.g. CocoaBindings and CoreData, their expressiveness is still quite useful.

-- RobRix

----

Well, C-derived languages can always link with the Boehm GC but I am not sure about doing this on OS X since I think it requires some special linking feature in ELF which may not exist in Mach-O.  I seem to recall hearing that some GNUStep stuff uses this to get GC in Obj-C.

Eventually we will see some kind of GC come to the Obj-C world. Smalltalk + concurrency support on a JIT VM or even AOT-ed with a GC only - all we need to worry about is design and solving the main problem, not the details of the implementation, such as memory management.  The retain-release stuff is alright but it doesn't replace a full GC.

---JeffDisher

----

Boehm would seem to work fine on OS X. It's listed as a supported platform, and doesn't have any gotchas listed. (It used to not support multithreaded apps, but that seems to have been fixed.) Making Boehm work with Cocoa would seem to be fairly easy, by overriding +alloc and some friends, but there may be hidden subtleties.

----

I suspect it doesn't work.  Boehm is a conservative, mark and sweep garbage collector.  Suppose there are no other references to A or B.  We'd call these objects unreachable, but how is Boehm going to know it?  The     [NSNotificationCenter defaultCenter] object is reachable, and is holding onto a pointer to object B. A is registered to receive a notification from B, and the notification center has a reference to A.  Though I suppose the center might also have a reference to B, depending on how A registered.

----

*This is why you would have to unregister for the notification.  However, this is already the case since you should never be releasing objects that the notification centre still has a reference to.*

*Wanting the collector to make valid operations crash-worthy is probably not a good strategy since there is an uncountable number of valid programs which would be broken that way.  Any collector in a C-derived language would have to be exactly that:  a conservative, mark and sweep collector.*

----

You're confusing releasing with deallocating. Of course we release objects all the time that are registered to receive notifications - typically an object does not deregister itself until the dealloc method is called.  That will never happen in a mark and sweep system, so we'll have a memory leak.  On what cue do you suggest we deregister, if not in dealloc?

This is why NSNotificationCenter does not retain objects that are registered with it in standard cocoa.

*The GC would work by calling dealloc such that it would fully cooperate with the existing retain-release code.  This would be the same as Java GC calling finalize and it automatically supports this legacy functionality.  You would either need to rely on the current retain-release functionality or unregister when you are done with it (which is probably better than dealloc - dealloc is just a catch-all since we are generally lazy).*

When is the object supposed to deregister in my specific example with the two objects and the notification center?

**In other words, you have a chicken-and-egg problem. Your object won't be deregistered until it's garbage collected. But it won't be garbage collected until it's deregistered, because being registered means it's referenced, and still counts as live in the GC's eyes.**

----

The NSNotificationCenter problem could be fixed by using weak references to the objects. However, we quickly get into a situation where we have to go through all of Cocoa and find everything that stores a reference and relies on -dealloc to clear it. It seems like this would quickly become impractical.

*Cool.  I hadn't realized GC systems had such things.*

*There's no reason new, user-created objects won't have the same problems as standard cocoa objects. So the user must be ever vigilant. Not unlike vigilance in avoiding retain cycles in cocoa as it stands..*

----

Weak pointers in garbage collection: http://c2.com/cgi/wiki?WeakPointer

Weak (and soft, and phantom) references in java: http://mindprod.com/jgloss/weak.html

----

Ok, so here are some ideas for a hybrid ref counting/ gc system.

Basically it is the BDW algorithm, but instead of collecting all malloc'ed memory, it only collects from a set of objects that we specify. The reason behind this is so that the programmer has an option to use gc or not. 

So the garbage collector is an NSAutoreleasePool replacement, as someone suggested. Calls to alloc return a global singleton of the collector, say the class name is GC. The init method does nothing. 

Now, there is a global set S of potentially garbage objects. Whenever an object is sent the autorelease message, this add the object to S. Note this collector does not collect non objects (that might change). Instead, it assumes they are collected by the objects themselves. Anyway this is no worse than the current situation.

Now for the clever part (I think): each object directs the garbage collector. This makes more sense. Only the object knows for sure what it has and what it has not, so let the object tell the garbage collector. It does this using the visitor pattern. That makes sense too, since now you also have a universal interface for performing automatic deallocation, searching, marking and whatever else your memory manager (or other) algorithms need. 

so what you have is this method defined on every object:
    
- (void) visitChildrenWithVisitor:(<Visitor>)visitor
{
    [visitor visit:referenceField];
}

This visits everything referenced by the receiver with the visitor algorithm.
In the case where we have an c array of references, this can handle it easily. Or even XOR linked lists. Whatever you want. There can also be a default implementation that looks at the class definition to find references, or a bottom dollat impl that just looks for things that look like pointers.

Objective C can potentially be much more cooperative to a garbage collector than C or C++. Obviously, C routines are going to be left out, but this is early days, and we could write some wrapper object that does their collection, or use BDW-style pointer searching.

Now for the marking. For this, we are going to claim the lowest bit of the retain count. There is a global bit marking the colour of 'live' objects. The marker visitor visits all objects reachable from the root set, marking them with this colour, in the mostly concurrent way adopted by BDW. The sweep algorithm then deallocates objects from S that are not marked live. After garbage has all been collected, the colour swaps.

As for the pluggable algorithm, that is simple too. You just have an object that represents the algorithm, which defines a method collectGarbageWith:(<GarbageCollector>)gc

The BDW algorithm looks like this in my impl:
    
@implementation BoehmDemersWeiserCocoaVariant
- (void) collectGarbageWithCollector:(<GarbageCollector>)gc
{
	[gc buildInitialSearchSet];
	[gc concurrentlyMarkLiveObjects];
	[gc finishMarkingObjects];
	[gc concurrentlySweepDeadObjects];
	[gc flipAliveMarker];
}

This way, people can try out other algorithms fairly easily. Low level algorithm components are defined on <GarbageCollector> implementations.

Now, I think this can run fast, because the search is directed, safely, because the search is quite conservative in that it only garbage collects objects labelled as potential garbage, and flexibly because the user can choose to garbage collect or not, and can also change the algorithm if necessary. 

The search can also be less conservative than BDW in some situations since it has clearer information on what is a reference or not.

Now one very important bit I haven't covered is the stack/reg/globals search. This is all a lot easier on Mach than on other platforms since there are kernel functions for getting all the threads, thread stacks and registers, and the dynamic linker can tell us about globals.

So now the only problem is that no information about what is a reference and what is not is stored on the stack or registers. For this I am looking at the BDW implementation to get answers as to how to do pointer identification.

GNUStep uses the BDW collector, but GNUStep does not have a similar DO system, so we cannot compare their gc so easily.

These ideas are panning out nicely into an implementation, will post next few days, working or not.

Mike Amy

---- 

I do not understand how this garbage collection is supposed to interact with the cocoa retain count.  Does every object still have a retain count?  You say you've grabbed the low bit from the retain count, do you mean to override -[NSObject retainCount] to return _retainCount bitshifted by one?

*Right, every object still has a retain count, only now there is one bit less retain count. The lowest bit (the '1' bit) out of 32, is used as a marker. Every retain count increment is now a +2 increment to avoid changing this bit, and yes, this is overriding those methods, so already this is one problem, but not too serious except from the point of view of someone trying to extend memory management futher. Of course, if we can do this, we could also claim more bits, to mark other conditions, say if we are going to use a tri-colour scheme*

----

What causes the deallocation of an object?  Is it when the retain count drops to zero *and* the garbage collector decides it is a dead object?

*
No. Another point I missed, thanks. The GC set S is NOT searched during the concurrent mark phase. This is the point where retain|release counting ends and gc starts. What S is for is to hold on to the object in the same way NSAutoreleasePool hold on to them. Only this time, the object can only be released when nothing point to it. So what happens in the sweep phase is the sweeper goes through S and looks for dead objects (objects with a mark bit different fromt the current live marker. When it finds one, this is what it does:


*sets the retain count of that object to the highest number it can
*remove the object from the set
*deallocates it directly. 


Now the reason the retain count is set high is that if this is part of a retain cycle, that stops there being an error of an object being double deallocated by the cycle. Once the object is gone the retain count doesn't matter. Because of this behaviour, retain cycles can be fully deallocated by one member of the cycle being deallocated.

Actually, thinking about it if the retain count is just incremented by one it should still work, but setting it high is safer I guess.

*

So what, precisely, are the conditions that trigger the deallocation of an object?  Is it when the retain count drops to zero *or* the GC decides the object is dead with the caveat that the retain count of an object the GC has laid claim to will never drop to zero?

*
Objects still deallocate when their retain count drops to 0, which is how the hybrid aspect works. S retains these object so that collectable objects cannot have their retain count drop to 0 once the GC has control of them. The GC runs in a separate thread, and the trigger conditions are unspecified, that depends on the algorithm. 

I guess the best bet is to do it at the end of the event loop, when memory gets low or intermittently during a long running method that allocates a lot of objects, (the [S count] > maxGarbage being a trigger or lock condition for the gc thread)
*

----

Why are we particularly concerned with objects that get autoreleased?  I'd imagine that we'd want to stop putting autorelease statements in new code, since we supposedly have garbage collection in place..

*Like I said, it is a hybrid system, any objects that are not autoreleased can get deallocated as soon as their retain count reaches 0, which they can since they are not retained by S.*

What I mean is _why_ not what is it.  What does this hybridization buy us?  We still have to search the entire world for object references, so why don't we just garbage collect everything?  How do the existing retain counts make anything easier at all?  (That is, you can make retain, release and such no-ops and do all the memory management yourself.)  I don't see how this system is any stronger than just dumping in BDW straight, or how it avoids any of the pitfalls discussed above.  But we do have to do some weird stuff, like autorelease an object just to get it put under automatic memory management.

*If you are going to mark-sweep the whole lot, then you are going to have problems with DO, since you can't easily search the memory on another machine. With the hybrid system, DO can still use ordinary retain release. Actually the GNUStep gc allows you to specify variables which are excluded, which could fulfill the same role. Also, since you don't have to worry about retain cycles, you can now use the replace function (RetainReleaseTips) for all reference updates, so you don't have to think so much about retain|release so much.*

---- 

Do you mean to check only live objects in the set S for references to garbage-collectable objects?  I don't think that can work.. 

    

NSMutableArray *arr = [[NSMutableArray alloc] init];

[arr addObject:[NSNumber numberWithInt:3]];



Bam, the only reference to the created (autoreleased) number is within an object that isn't in S.

*

No, S is not the search set, S is the potential garbage. The root set is the initial search set, and grows with iterations of the concurrent mark phase, until few enough new references are added that it can briefly stop the world and finish the mark from those few references, like BDW. Then the concurrent sweep phase takes over, which looks for objects in S that are dead (mark bit is not the same as the global 'live' bit). These objects are forcibly deallocated. S is not allowed to be searched and marked, this means that the garbage collector just excludes itself and its thread from the mark phase. S is not a real global, it is an ivar of the GC, so it won't be in any data areas either, so this exclusion is OK. S limits which objects can be collected to objects that would have been autoreleased.

One problem I see with this is that you need to deal with objects that have become dirty during the concurrent mark, phase, ie, whilst the marker is running in a separate thread, the mutator changes some references, these now must be searched before the sweep phase otherwise this could lead to live memory being deallocated. 

There are several ways round this. I believe BDW uses virtual memory hooks to mark whole pages as dirty. That seems general but inefficient since a whole page must be searched. Printezis and Deflets implementation uses extra code with every pointer mark things as dirty quicker. Or you can just do this: make sure the marker never searches from already 'live' data, which it shouldn't anyway, then, as described above, then, near the end of the concurrent mark phase, when there are few new refs per iteration, stop the world and do a full search of everything in the search set, for new references. This is not too bad as you are not going to have any updates from the mutator and most things are already searched. You can use a different and more aggressive marker to do this quickly since you don't have to worry about concurrency. So now you have all the objects marked, and you can be pretty darn sure that anything in S that is not marked live is really dead, unless some object has neglected to allow visits to some reference. So these things are deallocated.

Of course this implies that the gc must run in its own thread and probably not autorelease anything it owns, might cause a leak since they might not be searched.

There is another way around the reference update problem - get the object to mark itself as dirty every time it updates a reference. This is just the setting of one bit, which is a tiny cost, but is magnified because updating references is so fundamental. I don't know which way will be more efficient, this system or the stop the world and check system. Printezis and Deflets say this system is more efficient generally. I imagine it will depend on the programs characteristics regarding garbage generation and reference mutation rates. For this I start thinking about use a heuristic algorithm to decide which gc strategy to use, but anyway it is hard to make work because it will be hard to change the way reference updates take place in software that has not been designed that way. I would favour the former solution as less intrusive.

So the major problem with this system is that it does not completely solve the gc problem, it is still possible to create a retain cycle if nothing is autoreleased. Solutions - autorelease anything in the retain cycle, or, be less conservative about what can be collected now we are talking about overriding alloc, NSAllocateObject, like malloc is overridden like in BDW. These would be overridden such that whenever any object is allocated it is added to S. Not too hard to do that. That would catch almost everything. Or have I missed something? 

MikeAmy

*

----

A slightly kludgy way to implement garbage collection is to define a new operator (call it     := for now) that automatically     autoreleases and     retains. To use it, you would just have to replace all "    [x autorelease]; x = [y retain];" with "    x := y". The only problem I could see is a possible problem using it in an expression, unless you made it a function instead of a macro.

Hey, that could work. Anyway, I'm not going to be the one to implement this, but something like this could work (with the correct syntax -- I don't know how to do this...)

    
#define (a):=(b) _assignGC(&a, b)

_assignGC (id *dest, id source) {
[dest autorelease];
dest = [source retain];
return dest;
}


--JediKnil

*This idea is just automatic reference counting, and it will not work on anything beyond toy problems. If A has a reference to B, and B has a reference to A, then neither one will ever be deallocated.*

It seems to me like real garbage collection needs to have access to every object's references...it would be hard to implement GC with existing Cocoa frameworks. Maybe you would have to write wrapper classes for everything in some new version of Objective C... -JediKnil

----

Ok, so another problem with the ideas I have outlined above is still this: This technique of object directed searching works fine for objects, but Cocoa is supposed to be OK with C programs, so how are you to search non objects. Now we fall back on the lowest common denominator and have to use a BDW style gc for doing that, especially if those programs are using pointer arith. Objective C might be cooperative, but C aint. Solutions: everything is an object or inside an object. That means you could wrap malloced memory inside an NSData like structure and search that. Another solution: all potential garbage must not be passed to unknown C routines. lol, we could call the new language ObjectiveNoC -- MikeAmy

Another issue that BDW deals with is that of pointers to the middle of objects - MikeAmy

----

Doesn't PyObjC have garbage collection, and doesn't it do it by automating reference counts? Seems like we could "backport" this to objC...

----

As far as I know, PyObjC uses Python's garbage collector for Python objects, and normal Cocoa refcounting for Cocoa objects. This works because it's only the interface between Cocoa and Python objects that get automatic refcounting, so no cycles can develop.

Automatic reference counting by itself *does not work*. It was probably the first idea tried for automatic garbage collection, and it can be a helpful aid for GC, but it will always lead to leaks, or force an extremely restricted design, if used by itself.

----

Indeed, look how it restricts the target action paradigm objects, like NSControl, and also NSNotification observers. These objects have to removed before they are deallocated. Also something else has to hold on to them, leading to design dependencies ie restrictions. -- MikeAmy

*I'm not sure why "indeed" is at the beginning of your paragraph. You're talking about manual refcounting, I was talking about automatic (compiler-generated) refcounting. All memory management strategies come with design restrictions, but automatic refcounting's limitations are so severe that it is basically impossible to do anything useful in such an environment.*

Oops, yep, I missed your meaning. 

----

Hey that gives me an idea, if PyObjC can do this, why not define a level of objects above ordinary objects, with the same restrictions as python objects in PyObjC. That way we can employ the object directed gc as above, but only on these objects. Ordinary Objective C objects still have the old system

*Cool. Then we can change the syntax for dealing with the objects in the new system to differentiate them from the old style. Let's call it "Python".*

----
As requested by MikeAmy, a project which is described as "doesn't work yet" is up at http://www.mikeash.com/cocoagc/toyGC.tgz -- MikeAsh

Well, it was promised working or not, and this should be easier than question answer style. Yes, important bits are missing. :( -- MikeAmy 

----
http://developer.apple.com/documentation/DeveloperTools/gcc-3.3/gcc/Garbage-Collection.html

*That document doesn't apply to the Apple/NeXT runtime. False alarm everyone.*

----

Maybe it's time to rekindle this topic...

http://www.mulle-kybernetik.com/weblog/archives/2005_04.html

last article on the page. - AlexClarke

----

Someone wrote:

#  Mark and sweep garbage collection is notorious for negatively impacting interactive application usage. e.g. it adds unpredictable latency to user interaction.
# Mark and sweep garbage collection typically requires a larger working set in RAM. Will you need still more ram ? 

People often have this mistaken assumption that manual storage management has low and bounded latency, uses memory efficiently, and is fast.  All of those assumptions are badly wrong.  Manual storage management has unpredictable and unbounded latencies, it leads both to much higher fragmentation of memory and much higher retention of unneded memory than automatic storage management (in particular in C-derived languages, where objects can't be moved around by the GC), and it usually requires more cycles per byte allocated than a good garbage collector.  Manual storage management is one of the main reasons for C, Objective-C, and C++ applications to be as bloated and slow as they are.  And, unfortunately, the problem is unfixable in C-derived languages--Boehm GC is pretty much the best you can do in terms of GC for C-derived languages, and Boehm GC isn't very good compared to the best GCs.

Cocoa will never have GC.  Short of a complete rewrite, the best it will ever get is good bindings to languages with GC (like Java, C#, Python, ...).

*And this is why there's a -finalize method on NSObject, and why it's public knowledge that Apple is adding a GC to Cocoa?*

----

Should be deleted?
----
Nah... I say keep it, for a while at least. It's good to know just how wrong we can be.

----
haha, we'll just wait until the garbage collector picks it up. *drum cue please*

----
I think the jury is still out on Objective-C 2.0 Automatic Garbage Collection.

There are still some unhandled edge cases (that may be improved) like the following: http://www.cocoabuilder.com/archive/message/cocoa/2007/11/9/192716

There are still issues (that may be improved) as documented in Apple's release notes:

*
The Core Graphics APIs (Quartz 2D) see an approximately 25% reduction in drawing performance for applications compiled to use garbage collection. For that reason, enabling garbage collection is not recommended for applications making heavy use of 2D drawing.

There is a known issue with garbage-collected applications using Core Image to process frames from Core Video; the frame memory does not get collected, and the process eventually runs out of address space. If your application processes video frames using Core Image, enabling garbage collection is not recommended.
*

Some of the following may be handled (I don't know yet), but I have lingering concerns:
 
*does it work well with distributed objects?
*does it negatively impact interactive application usage? (beyond 25% reduction in some drawing performance)
*does it typically require a larger working set in RAM?
*does using it with the millions of lines of existing C code often require detailed knowledge of how to selectively enable, disable, and tune the collector behavior. Such knowledge is IMHO more arcane than using Cocoa's reference counting.
*does it work for memory and other resources allocated from sources other than the heap?  Do we still have to learn reference counting to know when you can reuse graphics card texture memory or relinquish control of a socket or communicate over a language bridge or use distributed objects or use POSIX semaphores or use shared memory?
 

-- Erik Buck

----
I'm going to make a stab at these, although it's largely guesswork at this point.


* I think it should work with DO, with the exception of not being able to free reference cycles which cross the DO connection. This will merit more care and attention to DO objects but that tends to be the case anyway.
* The collector shouldn't impact interactive application usage, but of course collectors by their nature tend to be nondeterministic. The collector only pauses threads for a short while, and does most of the processing with the app's threads unpaused. The pauses should remain below the threshold of human perception. As far as the 25% thing goes, that number is utterly ridiculous. The performance questions raised by GC are complicated and there's simply no way that a single number such as that could carry any meaning.
* Most apps will probably have a larger working set in RAM, however apps which allocate a great deal of memory between returning to the event loop could very well end up with a smaller working set, especially if they aren't careful with the autorelease pools.
* The collector will not touch memory that doesn't involve ObjectiveC objects unless told to. Your C code can continue using malloc/free and such techniques with no changes. You will have to be careful to avoid passing collected memory, such as pointers to NSData storage or NSAllocateCollectable unless you're pointing to it from GC-aware code, but that's not too different from the state of things now where you have to avoid passing autoreleased memory around.
* Control of other resources is a sticky point. You can always wrap them in objects which free the resources in their finalizer, but the problem is that the finalizer does not run at a deterministic point in time, so if the resource is scarce, which many of these are, it may not be freed soon enough to avoid starvation. Apple encourages us to explicitly free such resources rather than waiting for a finalizer, but doing so either requires a strong concept of who owns the object (and therefore knows when the object is no longer needed) or reference counting so that multiple objects can share ownership. This is again similar to the old situation where autorelease could make it unclear when an object would go away. As far as how language bridges interact with the GC, I really have no clue there.


-- MikeAsh

