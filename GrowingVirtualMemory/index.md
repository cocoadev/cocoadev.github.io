---
layout: page
title: GrowingVirtualMemory
---

I have been working on an application that does the following operation.

The application has long running NSTask that communicates with the server
to send/recieve the data to/from the server and correspondingly updates the User Interface elements.


During this run, the size of the virtual memory keeps on increasing. 

The virtual memory grows beyond 15 GB in the long run.

The system cannot satisfy the virtual memory needs by allocating additional memory, when there is no space in the disk.

What is the reason for this growing virtual memory usage and how can it be avoided in code ?

Most of the objects in the loop are autorelased .
Does it relate in any way to AutoRelease pools?

I appreciate in advance for any help.

----

Show your code.

----
Virtual memory has nothing to do with anything. Virtual memory is only allocated *address space*. Now, from your description it sounds like you're actually using it, but there's nothing wrong or bad about an enormous VM size by itself.

----
This should really have been posted on the forums, but it might as well just be answered here.

Autoreleased objects are not actually released until the autoreleased pool they are in goes away. Now, the only set places for autorelease pools to go away are at the end of an event cycle or when a thread terminates, so probably everything in your pool is sticking around during the entire communication. It might be a good idea to allocate and release an autorelease pool every time (or every X times) through the loop, to make sure the memory you're not using is being properly reclaimed.

--JediKnil

----
And just a note, there's almost never a point in the "every X times" approach to autorelease pools. Autorelease pools are *fast*. If you're allocating more than a couple of objects per loop, the cost of the extra memory pressure from keeping them alive longer will outweigh the cost of the pool. If you're just allocating a couple of objects per loop then there's probably no speed worry anyway.

----
Even better, if the objects are short-lived, just alloc/init them, use them, and then release them. Ex:

    
MyObject* foo = [[MyObject alloc] init];
[foo setSomething:@"Some value"];
[someDictionary addObject:foo];   // The object is now retained by the dictionary itself.
[foo release];


As MacOSX's memory system has pre-allocated entries for various sized memory objects (cached for re-use), this all is super-fast.

There are so many cases no auto-released objects are needed, but due to past behavior they sneak in....

Going back to the original question, another possibility is a memory leak somewhere. Check your memory object usage.

----
You cannot guarantee that no autoreleased objects are created unless you only use your own classes and never use Cocoa's. In your example, the -setSomething: method might autorelease the old value. In general, autoreleased objects can happen even if you alloc/init/release everything. In a tight, long-running loop it's always a good idea to create/destroy an autorelease pool for each pass.

----

Problem with growing virtual memory is that, it takes up the disk space  in the long run leaving zero disk space so that the application can not run.

Getting the following error after long run :

 malloc: *** error: can't allocate region
 malloc: *** set a breakpoint in szone_error to debug

----
Taking up disk space is a problem with growing memory usage, *not* a problem with growing virtual memory. Virtual memory just means the address space is allocated for *something*. It could, and often is, be completely un-backed by anything, or be mapped to an existing file.

Once malloc starts failing that generally means that you've filled up your address space, however, and this is bad. But this is unlikely to happen at 15GB or anywhere near; either it will happen near 4GB as you fill up your 32-bit address space, or you'll go much, much farther in a 64-bit process.

----

Use Apple's debugging tools - run Instruments so as to see if/when/what/where there is a memory problem.

