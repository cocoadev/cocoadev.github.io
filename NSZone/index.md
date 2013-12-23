---
layout: page
title: NSZone
---

**NSZone**

== Deprecation Warning ==
**According to the NSObject Class Reference, in the documentation for allocWithZone: , "memory zones are no longer used by Objective-C.". Which rather heavily implies that this entire article is now moot.**

* part1 - The basics
* part2 - The functions
* part3 - Supporting zoning
* part4 - NSZone wishlist


----**part1 - The basics**----

NSZone is Apple's way of optimizing object allocation and freeing. NSZone is not an object; it is an opaque C-struct storing information about how memory should be handled for a set of objects.

One rarely needs to worry about handling your own zones in applications; Cocoa handles it transparently. A default NSZone is created on startup and all objects default to being allocated there. So why would you want to use your own?

If you are mass-allocating hundreds of cheap objects, you may find the cost of actually allocating space for them becomes significant. Because the standard zone is used all the time, it can become very patchy; deleted objects can leave awkward gaps throughout memory. The allocator for the standard NSZone knows this, and it tries to fill these gaps in preference to grabbing more memory off the system, but this can be costly in time if the zone has grown quite large.

If you want to mass-allocate objects, then, you can create your own zone and tell it not to bother with finding gaps to put new objects in. The allocator can now jump to the end of its allotted memory each time and quickly assign memory to your new objects, saving a lot of effort.

Allocators can save you time elsewhere, too, as asking the OS for more memory, which a zone needs to do whenever it fills up, is another costly operation if it's done a lot. Much quicker is to ask for huge chunks of memory at a time, and you can tell your NSZone what to do here as well.

Rumor has it that NSZone could save you deallocation time in the Good Old Days, too, with a method that simply chucks away all the allotted memory without bothering to call deallocators. If a set of objects is self-contained, this could save a lot of time, as you can chuck them all away at once without tediously deallocating them all. Alas, there appears to be no sign of this godsend in the current documentation; the single NSZone method (NSRecycleZone) carefully puts all the objects in a zone neatly on the default NSZone. Not exactly a huge time-saver.

So, in summary, zones save you time in mass allocations. But only if programmers know how to use them!

----**part2 - The functions** (AKA more basics)----
See also: http://developer.apple.com/techpubs/macosx/Cocoa/Reference/Foundation/ObjC_classic/Functions/FoundationFunctions.html

    
NSZone ***NSCreateZone**(unsigned int startSize,
                     unsigned int granularity,
                     BOOL canFree)


Creates a new zone. *granularity* specifies how much memory the zone will grab at once, and correspondingly how much needs to be freed before it will shrink. *startSize* warns the zone how much it can expect to handle straight away.


*canFree* gives us the optimization mentioned above: if NO, the zone will not release any memory until it is recycled by NSRecycleZone. This supercharges NSZoneMalloc at the cost of massive memory expenses if objects are frequently allocated or released in the zone. Very useful if loading a large number of unchanging objects, for example the sprites and creature types in a game level.

    NSZone ***NSDefaultMallocZone**(void) (defaultZone)

Returns the default zone allocated by Cocoa on startup. This zone is used by the C function malloc; it is also used by NSObject's alloc, and by any NSZone-using method passed NULL instead of a zone. Almost certainly allocated with canFree YES, this zone can be expensive to create objects in.

    void **NSRecycleZone**(NSZone *zone)

A distressingly impotent function for releasing zones, this method takes time out to transfer all the objects in *zone* to the default zone before releasing the memory *zone* took.

NSRecycleZone in Leopard is a noop:
    % otool -toV /System/Library/Frameworks/Foundation.framework/Foundation | grep -B 0 -C 4 NSRecycleZone
_NSRecycleZone:
00160b40	pushl	%ebp
00160b41	movl	%esp,%ebp
00160b43	leave
00160b44	ret
% otool -toV -arch ppc /System/Library/Frameworks/Foundation.framework/Foundation | grep -B 0 -C 1 NSRecycleZone
_NSRecycleZone:
00133340	blr

    void **NSSetZoneName**(NSZone *zone, NSString *name)

Sets the name of *zone*; can aid in debugging, according to Apple.

    
void ***NSZoneCalloc**(NSZone *zone,
                   unsigned int numElems,
                   unsigned int byteSize)


Allocates memory for an array of *numElems* elements, each of size *byteSize*, from *zone*. This memory is initialized with zeroes. If something prevented memory allocation, this function will return NULL.

    void **NSZoneFree**(NSZone *zone, void *pointer)

Returns memory to the *zone* from which it was allocated; the C function free does the same, but needs to determine the zone of the pointer first. Not necessary to call this directly for objects; NSObject will automatically free the memory used when an object is deallocated.

    NSZone ***NSZoneFromPointer**(void *pointer)

Returns the zone currently housing a chunk of memory. For an object, use NSObject's zone method instead.

    void ***NSZoneMalloc**(NSZone *zone, unsigned int size)

Allocates a chunk of memory from *zone*. Probably not necessary to call this directly; instead use NSObject's allocWithZone: method to make room for an object. If something prevented memory allocation, this function will return NULL.

    NSString ***NSZoneName**(NSZone *zone)

Returns the name of *zone*, or nil if no name has been associated with the zone.

    
void ***NSZoneRealloc**(NSZone *zone,
                    void *ptr,
                    unsigned int size)


Changes the size of a previously allocated chunk of memory belonging to *zone*. If the memory cannot simply be extended to the new size, new memory may be allocated, in which case up to *size* bytes are copied verbatim to the new location. If *ptr* is NULL, the function acts like NSZoneMalloc. If something prevented memory allocation, this function will return NULL.

    BOOL **NSShouldRetainWithZone**(id <NSObject> anObject,
                            NSZone *requestedZone)

Returns YES if *anObject* should be retained for use in *requestedZone*, NO if a copy should be made with copyWithZone: or similar.

    - (NSZone *)**zone**;

Returns the zone of the receiving object.

    + (id)**alloc**;

Allocates an instance of the receiving class in the defaultZone.

    + (id)**allocWithZone:**(NSZone *)zone;

Allocates an instance of the receiving class in *zone*. Properly coded objects will ensure any memory they allocate that will last their lifetime is allocated in the same zone.

    - (id)**copy**;

Creates a duplicate of the receiver, stored in the defaultZone.

    - (id)**copyWithZone:**(NSZone *)zone;

Creates a duplicate of the receiver, stored in *zone*.

----**part3 - Supporting zoning**----

Alas, supporting the wonders of zoning is not free. If your objects use other objects to support them, or if they allocate memory directly, you will have to consider how to fit zoning in.

Let us suppose we are writing a matrix object; each matrix keeps a hashing object and a large chunk of memory, and supports enumerators. Let us consider each in turn: 

* The supporting object

Clearly, the supporting object is an integral part of the matrix; it lasts for as long as the matrix does, but no longer. So we conclude, quite rightly, that this object needs to be in the same zone as the matrix. If you create hundreds of matrices in a special zone with freeing off, it would be quite irksome to find your optimizing rendered useless because each matrix was wasting time allocating a hashing object in the default zone.

If the supporting object is passed in as a parameter to a matrix initializer, we should check if we need to re-zone the object by calling NSShouldRetainWithZone; if it returns YES, we can simply retain the object and store a reference to it; if NO, we must call copyWithZone: and store that. This prevents lunacy.

* Directly allocated memory

As with the supporting object, we must create our chunk of memory in the same zone as the matrix. One of the zoned malloc/calloc above will do the trick nicely.

* Enumerators

At first blush, it might seem enumerators should be in the same zone as the matrix; after all, they are very closely related. If the matrix were released along with its zone, the enumerator shouldn't be far behind.

This line of reasoning forgets one of the crucial reasons for zoning: fast allocation. Enumerators are inherently short-lived, and in a zone without freeing this means lots of wasted memory. It has to be up to the owner of the matrix to be sure they don't release the zone before all enumerators are released. The best place to create enumerators is the defaultZone.


So what rule of thumb should we apply? *An object should only allocate things with roughly the same lifespan in its zone.* Clearly, there are exceptions. The matrix might allocate more memory for itself later on, for instance, leaving a hole where its memory used to be. But by and large the rule is clear-cut; in instances where it isn't, such as the matrix resizing, you should ensure memory is lost only in specific, unavoidable methods, and you might even go as far as to say so in the class notes. (Note that resizing a chunk of memory is probably alright immediately after it has been allocated - say, in a constructor where the number of arguments is variable.) Then it is up to the user of the class to avoid these methods when they will prove to be costly.

This leads neatly on to another point to be made about supporting zones properly. *Always support copyWithZone:.* Furthermore, always do it properly; even if you think an object will never change, if the zone is different then copy it! Supporting this allows other objects to do what our hypothetical matrix did with its hashing object: support zoning correctly. Note that supporting copyWithZone: requires conforming to the NSCopying protocol as well as implementing the method.

Finally, *Be zone-friendly in your documentation.* State what parameters may be re-zoned, and what will be left well alone. Documenting the behaviour will not ony help anyone who uses your class later (including yourself!), it may also help you ensure the behaviour of your class is consistent. Zoning is not just for a rainy day. It can be a good habit to get into.

----**part4 - NSZone wishlist**----

Alas, Apple has not made NSZone the answer to memory management that we might have liked. Here's a list of gripes about Cocoa zoning; anyone wishing to recommend solutions (or other complaints) feel free.


* Firstly, zones no longer allow one to mass-deallocate objects without messing about with actual deallocation. For anyone who accidentally deletes a zone with live objects they wanted to keep, this is a blessing; for those who actually liked this feature for its speed, a curse. rdar://problem/4444029

* Zones are thread-safe. Sounds good, no? Wrong. Thread-safety is expensive, and there is no way of turning it off if, say, you've specifically allocated one zone per thread for efficiency's sake. This potentially large timesaver is therefore dead. Are there any efficiency measures in place to stop thread-safety being a huge efficiency sink? No.

* There is no way of extending Cocoa's zones to use custom memory-management strategies, which is a shame because then we cunning developers could create a wide range of different zoning tactics, all open-source and well-documented. Without that, custom memory-management can only be done for custom classes. CoreFoundation has the potential, but NSZone is an opaque structure and not open-source.

* Even if there were, the Darwin zone-handling code used in MacOSX is not thread-safe! It works fine if one doesn't create or delete zones, but trying to use many zones to manage your application's memory use would open you up to a host of ThreadSafety issues dormant in Darwin.


-- KritTer, comments welcome

I believe I know why the fast-deallocation would be dangerous. Here is what the Apple documentation says about how NSCopying can be implemented:

*
Implement NSCopying by retaining the original instead of creating a new copy when the class and its contents are immutable.
*

Suppose I have an object "foo", allocated in zone "bar". I then do: a = [foo copy]; [foo release]; and then delete the zone. If "foo" was an immutable object, it would still remain in zone "bar" after the copy, which means that deleting the zone would destroy the instance. There may also be other cases a fast-deallocation could destroy information without the developer being aware of it.

-- EliasMartenson

Rather, that's why copying should verify the zones are the same before merely retaining. It's a catch-22: NSZone isn't used, because NSZone can't be used, because people have been coding in ways that break NSZone<nowiki/>s, because NSZone isn't used. -- KritTer

----
Does anyone here know the implementation of the NSZone struct itself? I'm porting Cocoa to Windows (the .net framework should make that an easy task - I already have a lot of commented functions and had to change the name of the NSObjectProtocol (a protocol by the name of NSObject also exists) to NSObject_P to avoid CSharp (C#) name clashes). - PietroGagliardi

----
Zones are basically unused and useless, so I'd just not bother implementing them.

----
Thanks for the info. I'll just implement a basic NSZone that throws an exception on creation. I hope to get enough done soon so that I can present a CTP, although all you'll get that far is the FoundationFramework. - PietroGagliardi

----

I thought you were going to clone Photoshop.

Anyway, don't make it throw an exception, because that will break any code that actually does try to create a zone.

----

I wanted to use zones to destroy a lot of objects quickly. Looking at the Apple Docs (https://developer.apple.com/documentation/Cocoa/Conceptual/MemoryMgmt/Concepts/Zones.html, revised 2007-06-06), they reference malloc_destroy_zone as providing this functionality, although Xcode's API help doesn't show it up, and it doesn't show up in 'man' either, although it is present in /usr/include/malloc/malloc.h on my stock 10.4.10 system.

Has anyone used this function successfully?

----

Zones are not really the answer; you'll free the memory held directly by the objects (i.e. the memory pointed to by the id pointers) but the objects will not be properly deallocated, so their fields may remain alive and cause leaks. If you want to keep track of a list of objects which you then forget about all at once, you may want to use an AutoreleasePool. Other approaches could involve an NSMutableArray or NSMutableSet or similar.

----

Hmmm. Well, malloc_destroy_zone kind of works. My objects are actually plain old C structs, although they have a couple of fields that are NSStrings right now. In the current dataset, there are 1.8 million of them, in a tree. Obviously, I don't want to walk the tree free'ing and releasing everything, as that would be a pain. I'm actually pre-allocating a big block (about 50MB) of RAM to hold the objects themselves, and allocating child arrays and a few other bits and bobs on the fly - in theory I could pre-calculate the size, and allocate it all as one huge block, and then just fill it in as I read in the data.

Apart from the NSStrings of course.

Right now, within the block, everything is allocated with NSZoneCalloc/NSZoneMalloc or allocWithZone for the NSStrings.

As I load the data, Activity Monitor shows Real Memory climbing from 20MB baseline to 220MB+. When I call malloc_destroy_zone, Real Memory use drops to 80MB, so something is clearly getting free'd, and something is hanging around. Repeatedly loading the data leaks memory at about 60MB a time.

ObjectAlloc doesn't seem to spot that the any of the zone'd memory has been free'd up for some reason.

heap is actually a lot more useful, and does show some zone info. It seems to think that my 1.8m NSStrings are occupying 63MB in the default zone, even though I allocWithZone'd them at creation - even the base memory for them doesn't seem to get freed (they're created with initWithCString:encoding:).

So, it looks like zones work, but not for Obj-C objects. That's not so bad for me, as I can use C strings instead of NSString's, and zone's are still kind of useful, as I don't want to be stuck with a block of contiguous memory. It would still be great if they worked as one might have hoped.

----
I'm curious as to why it would be a pain to free the entire tree node by node. This seems to be a case of confusing the work that the computer will have to do with the work that the programmer will have to do. A recursive function to free an entire tree should be quick and painless to write and will solve this whole thing much better than zones will.

----
Painful for the user, not the programmer. Because in my case I have so many objects, I actually do need to be quite conscious of memory usage and performance. I don't really want the app to hang for a second or two while the tree gets free'd.

----
If you have so many objects that freeing them will be a concern, consider allocating just the C structs in a zone.  Then walk the tree iteratively and free all the Cocoa objects, followed by a mass free of the zone used for the structs.

----
Have you measured the performance yet? This sounds like a case of PrematureOptimization. If you have and it really is too slow, how about spawning a thread to do the destruction? It's not like it's something which must happen before anything else can proceed.

