---
layout: page
title: UnAutoreleaseing
---

Is it possible to say something like:

    
- (void)someMethod
{
     General/SomeThing *some = General/[[[SomeThing alloc] init] autorelease];
     // do something
     if (someCase) // then I don't want the instance 'some' to be autoreleased
          [some unautorelease];
}


Is this a pipe dream?
In General/NSEnumeratorsIndex there's a point that's raised that makes it particularily desirable to have this behavior at a low speed cost to save memory.  Namely, General/NSEnumerators are autoreleased, and it would be nice to kill them when you know you're done with them instead of waiting for the run loop to do GC for you... 

*Pipe dream. Searching through the autorelease list for the object in question then removing it will take longer than the time it adds on to autorelease's GC time. Just make a new autorelease pool if you want to keep memory costs low :(*

Figured as much.  What are the general guidelines for maintaining multiple autorelease pools?

*How do you mean? Nest them properly, and don't abuse them. Find a tool that monitors memory usage and see if you will actually benefit from a pool.*

Well... what I'm worried about is have a methodA call a methodB that returns something autoreleased, and methodA is counting on that return value to stick around for the duration of methodA's execution, except that there's a pool that is sandwhiched somewhere between them, and the return value of methodB is released prematurely... are there established conventions and rules of thumb to follow so that that kind of thing doesn't happen?

**I may misunderstand your point, but could you not just send the enumerator a retain message?  It will still get autoreleased, but it won't deallocate until you send it another release message.**

The point is to deallocate the enumerator IMMEDIATELY, instead of waiting for it to be autoreleased.  If you're dealing with any kind of loop or recursion where an object get's autoreleased, before long you'll have a pile sky high of autoreleased object carcasses... and that pile will start to stink pretty baddly when you run out of memory!  :) The point is that if you know you will no longer be needing an autoreleased object, you should be able to kill it right then and there instead of waiting for GC.

    Well if you want to do that you should creat local autorelease pool and release it whenever needed. Then your enumerator will be released too.

*Further, your proposed "problem" is not actually an issue. If methodB contains that autorelease pool, it should make sure to retain the object it wants to return before releasing the pool! It can autorelease it (on an external pool) just before it returns. Alternatively, if methodA is doing this autoreleasing before it even uses the value, shame on it! A similar approach will solve the problem.*

**This is a classic example of General/PrematureOptimization. Don't do this. Autorelease pools work. Let them do their jobs.**

You're not likely to create too many enumerators in a loop, so enumerators aren't likely to be a problem in any event. Generally you're looping through the objects in an enumerator, and they're not autoreleased. If you are creating many enumerators in a loop, there's probably a better way to do whatever you're doing.

*Autorelease pools also use that memory cost to help speed up programs: since all the objects are deallocated at once, large gaps appear in memory, and memory allocation can be quicker.*

**Not deallocated at once, released at once. I think the assertion that they help speed up programs needs to be studied somewhat.**

The "Large Gaps" is kind of specious reasoning.   There's no guarantee that the objects being released are near each other in the address space.

*Oh, I'm well aware, hence the use of the word 'help'. However, in a normal iteration-loop, almost everything created will be placed on the autorelease pool, so if memory is largely unfragmented the assertion follows. The more memory becomes fragmented, of course, the more it will stay that way.*
