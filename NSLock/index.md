---
layout: page
title: NSLock
---

What is the correct way to use NSLock?

a) enclose     [lock lock] and     [lock unlock] around a block of code with multiple variables that you want to keep safe, or
b) enclose a different     [lock lock] and     [lock unlock] around EACH variable that you want to keep safe.

Can someone help clarify for me? Thanks --Kevin

----

I would use a lock for a cluster of interdependent variables. If several variables can be updated at one time, and the variables' values depend each other, it makes sense to update the cluster as if it were atomic, by using a lock to protect the entire cluster. That way, one thread is not able to dip in and change just one variable while another thread is changing other dependent variables in the cluster.

----

Here's my page that describes how to use NSLock:  http://alienryderflex.com/NSLock.html

Question:  How does NSLock's "lock" method work?  What keeps its code from suffering the same problems that you use NSLock to prevent?  To clarify -- suppose NSLock contains code that looks like this:

    
- (void) lock {
  if (![self isLocked]) {
    [self setLock:YES];
    return;
  }
  else {
    ...suspend the current thread and add it to a list
    of threads that are waiting on this NSLock object...
  }
}
 

What would prevent the above code from malfunctioning when multiple threads execute it simultaneously?  (And you can't put lock/unlock around NSLock's own code, as that would cause an infinite regress with stack overflow, and/or would require an infinite number of NSLock objects to implement!)

I'm super-curious to know what the lock method does...  please illuminate the issue if you can.  Thanks, --Darel Rex Finley

----

OK, I think I figured out the answer to my own question!  It could work like this (function names are made up):

    
- (void) lock {

  blockProcessorInterruptsForThisCore();
  if ([self threadId]==nil) {
    [self setThreadId:thisThread];
    unblockProcessorInterruptsForThisCore();
    ...dummy instructions that take at least as much
       time as the code executed so far in this method...
    if ([self threadId]==thisThread) {
      return;   //  successfully locked!
    }
  }

  unblockProcessorInterruptsForThisCore();
  ...suspend the current thread and add it to a list
     of threads that are waiting on this NSLock object...

}


Blocking interrupts (i.e. blocking preemptive multitasking for this particular processor core) guarantees that the
checking and setting of threadId will take less time than the "dummy instructions".  That way, by the time the dummy
instructions are finished, all setting of threadId, by any number of threads, will be finished (other threads will notice
that threadId is non-nil, and won't even try).  Then, the one thread that sees its own thisThread in threadId will get the
lock -- all others will be suspended.  --Darel Rex Finley

----
You can implement a simple lock by using an atomic test-and-set instruction, which modern processors implement. You can use test-and-set to build more sophisticated constructs, such as atomic increment, etc. To see what OS X already provides for you, see OSAtomic.h.

Once you have those, you can build a lock which operates entirely in userland unless there's contention. It would look something like this:

    
-lock {
   int32_t numThreads = OSAtomicIncrement32(&lock);
   if(numThreads > 1)
      SystemCallBlockUntilLockAvailable(&lock);
}

-unlock {
   int32_t numThreads = OSAtomicDecrement(&lock);
   if(numThreads > 0)
      SystemCallUnblockWaitingThreadOnLock(&lock);
}


Basically, use atomic operations to track the number of threads trying to get into the lock. If ever there is more than one, the extra threads call into the kernel which puts them to sleep and adds them to a queue. Then when a thread exits, it checks to see if any other threads are waiting, and if they are it calls into the kernel to have it unblock one.

I'm handwaving a bit on how the kernel will do its part, but it can use atomic operations, (very) temporary interrupt disabling, and if really necessary it can use a spinlock to ensure that is internal queue is modified safely. -- MikeAsh

----

Thanks!  (What's your name, BTW?)  I can see that an atomic (i.e. inseparable) test-and-set instruction would, all by itself, provide a way to get around this problem.  I didn't know that processors had such an instruction.  Surely the original x86 ISA doesn't have it ... so is it a new instruction in all modern x86 ISA processors (Pentium and beyond)?  --Darel Rex Finley

----

Stuff is now signed! I'm sure you're right about the original ISA. I couldn't tell you when it would have been added, but since the 386 was the first "modern" x86 processor (in the sense that it had a proper supervisor mode, MMU support, etc.), I would guess that it was introduced there. In any case, anything which can run OS X will have it. And of course the PowerPC has had such instructions since the beginning.

I've created the OSAtomic page with an overview of what it offers. Just be careful if you actually decide to use any of it, that stuff can burn. -- MikeAsh

----

Thanks again, Mike.  While you were posting that last reply, I found these:


*Intel IA-32:  xchg
*PowerPC:  lwarx/stwcx


They're signficiantly different in how they work, but both get the job done.  Anyway, it's good that Apple's wrapped it up in a neat thing like NSLock so I don't have to deal with it.  --Darel Rex Finley

----

According to Wikipedia, the LOCK and XCHG instructions have been part of the architecture since the 8086/8088. As for synchronization primatives, you should look at the pthread man page. Pthreads provides mutexes (which is what I suspect underlies the NSLock implementation), read/write locks, and conditions.

Carbon multiprocessing services (http://developer.apple.com/documentation/Carbon/Reference/Multiprocessing_Services/index.html) also provides critical sections, events, semaphores, and queues. - Stephen Butler

----

The pthread stuff underlies everything on OS X. NSLock and all of those Carbon things are layered on top of pthread primitives. In turn, the pthread functions are layered on top of those atomic operations and whatever kernel services they need. -- MikeAsh

