---
layout: page
title: OptimizingCocoaCode
---




Optimizing code is a very difficult thing to do, and there's no use claiming otherwise. With that in mind, here is some general advice, and some more specific tips, for optimizing Cocoa code.

Firstly, 99% of the time what you *think* will be a bottleneck actually won't be. To find bottlenecks, you must profile a running application, and probably the most Cocoa way of doing that is to use Apple's Sampler. This application attaches itself to running code and periodically glances at what it's doing; by identifying function/method names, it can tell you where your time is being spent, and hence what will present the best target for optimization. You might also have luck with the CHUD tools and gprof (when it works). http://goo.gl/OeSCu

Since you don't know where your problems will be, trying to design your code to be easily optimized will probably just waste your time. Instead, use sound ObjectOrientedDesign. You will be able to switch out bad code for good without disruption, provided you choose your classes sensibly. For example, wrap your data structures inside an object, so you can use a faster data structure if necessary; doing this with a C++ class adds no overhead to your running code.

Here are some specific tips for ways to speed up those pesky bottlenecks. They will work best in code called a lot, e.g. in tight loops.

-- crafted by KritTer and MarkDalrymple

----

There is a good set of articles on optimizing Cocoa code at http://www.mulle-kybernetik.com/artikel/Optimization/ ; it discusses optimizing FoundationCollections use, as well as more general Cocoa areas such as method calls and object allocation.

----

Another speed trick, when using Foundation objects (i.e. NSArray, NSDictionary), is using the functions provided by core foundation; for instance:

id obj = (id) CFArrayGetValueAtIndex ((CFArrayRef) myArray, 0);

is a lot faster than:

id obj = [myArray objectAtIndex: 0];

I have found that accessing data directly from a CFArray is slightly more than 4 times slower than using a C array (the Obj-C way is 9.5 times, the IMP cached method technique is 7 times): can anyone confirm this (not sure my sampling techniques are reliable)?

(I'd really hate to see much code "optimized" like that, though.  Kind of defeats the tastiness of Obj C and Cocoa.  And someday, the hardware and OS will be fast enough not to matter, even if it takes a few years. :) --LesOrchard)

(Well, remember, you don't want to optimize **all** of your code **all** of the time -- just the most important bits of it at the most important times. If performance means the difference between genius and crap, a CF trick like this can be just the thing. -- MikeTrent)

(Programmers will always find ways to use the increased power of processors and OSes. Concentrate optimizations on the critical parts of your program, those that are called frequently. -- DavidRemahl)

-- peacha

I'm not surprised at LesOrchard's numbers.  C-array is a pointer + offset + fetch.  The CFArray is the same plus function call overhead.  the NSArray is the same plus method dispatch overhead.  If you're doing a couple million array accesses, that overhead can really add up.  If it's once-or-twice per event loop, then it's not worth the effort to aim for a lower code level.  ++MarkDalrymple

----

Another form of optimization that probably comes up in OpenGL more than general coding is caching. In LodeStone, geometry is calculated from vector objects at runtime, and trying to do this for every single frame could bog things down if you had, say, a bloody lot of splines. So, I added a cache in the form of an OpenGL DisplayList (which is basically the easiest optimization possible with OpenGL--instead of calling a bunch of basic plotting functions, you compile them into a display list, and call that later on; it saves a significant amount of overhead).

I believe NSImage does the same thing with its NSImageRep instances; some network stuff is bound to also.

Some things to keep in mind with caching are when to update the cache, how long the cache needs to be around (e.g. should it be deleted after thirty seconds?), et cetera.

Caching isn't appropriate always, but if you're doing heavy calculations and there's a quick way of checking whether the data the calculation is based upon will be the same, it might be a reasonable idea. It's not hard, you just store the result of the calculation, but it requires a bit of thought as to when you should update that result.

-- RobRix

And be aware of locality of reference issues when caching.  If you cache big things to save on disk access (like pre-fetching a bunch of imagages), you could end up tripling the disk access (load into memory, image gets swapped out because memory is filling up, image gets swapped back in when it's actually used.)  A good technique is to separate the metadata from the cached data.  Fer instance, keep an array which has the image name, last access, is it loaded, etc, and then have pointers to the actual image data.  When you need to do your LRU check on the cache, you don't have to touch any of the pages that have the actual image data.

++MarkDalrymple

When creating a cache system from scratch, consider using memory mapping instead of writing files and having a separate RAM cache. This way the OS manages disk swapping for you and the only thing to worry about is the overall size of the cache and the eviction policy.

++ TomasAndrle

----

If speed is important in your apps, it may interest you to note that instantiating a class for the first time in your code carries a small performance cost, presumably as the isa information is created. This is circa 20ns for an NSDictionary, for example, so not very significant, but if you are writing very low latency methods, you should be sure to use the objects they init *before* calling them for the first time.

If you're *really* needing a fast run, but need to use dynamic dispatch, you can shave a few more ns off by calling every method your code will use beforehand. This caches the method IMPs, saving your code a little time when it needs it. Of course, if you are calling the same methods on the same objects repeatedly in time-critical code, it may benefit you still further to store the IMPs yourself in local variables, removing the need for multiple cache lookups; you can use NSObject's methodForSelector: to find these.

This advice is mainly for when timing very quick code, as you may otherwise introduce a significant error.

-- KritTer

----

I've thought for a while now that it would be worth Apple's time to develop an auto-optimizing compiler like Intel's ICC (or at least a new core for GCC on PowerPC).  The speed boost it would give could be useful in overcoming the common image of MacOSX as a "slow" system. --OwenAnderson

Definitely. And come up with a much better way of hooking assembler into normal code - there are so many bugs in their implementation, it makes my teeth ache :) -- KritTer

