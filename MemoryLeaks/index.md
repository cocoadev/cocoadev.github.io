---
layout: page
title: MemoryLeaks
---



**What are MemoryLeaks?**

MemoryLeaks happen when you goof up your RetainingAndReleasing in such a way that the RetainCount for AnObject doesn't reach zero.

If you allocate AnObject but never release it (remember, you have to release an object before it can be deallocated), chances are that sooner or later your used objects will begin piling up using more and more memory. That's called a memory leak, because the longer your application runs, the more free memory is "dripping" away.

**How to recognize and hunt down MemoryLeaks**

The nice thing about MemoryLeaks is that they don't crash your app immediately, so they leave you more debugging options than hard crashes. 

The bad thing: You have to look for them to see them. It's hard to overlook a crash, it's not quite so hard to overlook memory usage rising a few bytes each time a certain method is invoked.

(The first time I tried to track down MemoryLeaks in my application, I ended up finding one in Apple's own NSURL class instead - so it's no disgrace to create a leak. :) )

Newbies often write incorrect AccessorMethods that lead to MemoryLeaks - the AccessorMethods link from our CocoaSampleCode section has tips on how to avoid that.

There are a number of tools and techniques for debugging MemoryLeaks:


* TheLeaksTool (/usr/bin/leaks) is a UNIX tool that can help uncover memory leaks in your unix process. leaks works best when malloc's stack logging is activated via the MallocStackLogging shell variable (set the MallocHelp environment variable and run some command-line tool).
* MallocDebug (found in /Developer/Applications/Performance Tools) is, on the surface, a GUI equivalent of the leaks command-line tool. However, MallocDebug adds some features of its own. MallocDebug may be more suitable for those viewers who are less familiar with the command-line environment.
* ObjectAlloc (also found in /Developer/Applications/Performance Tools) checks just what instances of what objects are over-staying their welcome.
* The NSZombieEnabled and MallocStackLogging environment variables can be used to find out when deallocated objects are accessed, and when they were created, though maybe they don't work with TollFreeBridged types. See DebuggingAutorelease. If you're using NSZombieEnabled you can see false memory leaks in the Instruments (TheLeaksTool).
* OmniObjectMeter (found at TheOmniGroup's website, http://www.omnigroup.com/ ) is another option.
* You can check at any time an objects retain count. Simply do: [object retainCount] and it returns an integer.


**Java and MemoryLeaks**

One of the few advantages of using Java for Cocoa development is that you don't need to concern yourself with MemoryLeaks or RetainingAndReleasing, because Java handles all of this automatically for you with its automatic GarbageCollection.

*Although I'm a Java person, I'd like to point out that this does not mean that you have no MemoryManagement problems; you can still very easily end up with circular references that prevent either object from being garbage-collected (both objects have references to each other saved in variables, so the garbage collector cannot assume that either is unused). Supposedly the newer Java Virtual Machines also have better garbage collectors. --JediKnil*

That is not true. Circular references have not been a problem for decent garbage collectors for decades.

*Oops, sorry. I'm not sure exactly what I was thinking of; perhaps extraneous references? Such as when you accidently forget to set a array variable of 1000 objects to     null, and the GC still thinks you need to keep it around. But you're right that garbage collector technology has gotten much more sophisticated over the years. Maybe I was still thinking in the stone ages: automatic reference counting? --JediKnil*

Yes, automatic refcounting will have the problem you describe, which is why any half-decent GC will either add other techniques on top of it, or forego it altogether in favor of something else. Some early versions of various scripting languages (perl 4 comes to mind) used automatic refcounting instead of "real" GC and would leak if you set up a circular reference and didn't clear it manually.

