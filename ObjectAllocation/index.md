---
layout: page
title: ObjectAllocation
---

I have been engrossed in Cocoa for a time now...and obviously there is the continued problem of memory management. 

I was wondering, is there any way of showing how many times an object has been retained via an NSLog statement of some sort? For instance, I may have an NSArray and I want to check how many times it is retained. Is there some way of putting in a statement like NSLog([arrayName numAlloc]); or something similar?

----

I believe NSObject's -(unsigned)retainCount is what you are looking for.
    
NSLog(@"Array has been retained %d times\n",
      [arrayName retainCount]);


----

That will do what you want but be forewarned that this will not take autoreleasing into account.

--JeffDisher

----

I would also like to point out that calling -retainCount should *only* be used for debugging purposes. It can be useful for debugging, but you should never use it in real program code. (Almost: see WeakPointers.)

--MikeAsh

