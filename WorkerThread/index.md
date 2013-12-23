---
layout: page
title: WorkerThread
---

, 

**The problem**: My single-threaded application processes a lot of data, and the GUI becomes completely unresponsive during this period.

**The solution**: Create a *worker thread*, a piece of code that runs parallel to the main thread, allowing the latter to continue to handle user-interface demands. Here is a simple way to transform your single-threaded code to use a worker thread.



* First, you need to separate the data processing from the GUI updating, with a different method for each. Let's call them     processLotsOfData: and     presentDataToUser:. Where you put these methods is up to you. Just be sure to bundle up all the data to be processed in one object (passed to     processLotsOfData:), and all the resulting goodies that the GUI needs in another (passed to     presentDataToUser:).

Be sure to set up an General/NSAutoreleasePool within the processing function (i.e. in the     processLotsOfData: implementation); this is as simple as adding:

    General/NSAutoreleasePool* myAutoreleasePool = General/[[NSAutoreleasePool alloc] init];

to the start of the method, and:

    [myAutoreleasePool release];

at the end. (If you produce thousands of auto-released objects during your processing, you might want to take a look at the General/AutoreleasePool page.)

* Second, you need to fire up a General/WorkerThread. Check out General/NSThread: it provides the class method     +detachNewThreadSelector:toTarget:withObject:. Since we have the data processing all neatly bundled up, we can simply replace:

    [myObject processLotsOfData:dataObject];

with:

    General/[NSThread detachNewThreadSelector:@selector(processLotsOfData:)
                         toTarget:myObject
                       withObject:dataObject];

Be sure not to touch     dataObject once it's sent off to     processLotsOfData:, as the processing might have already begun. Also, be sure to *retain* it before calling processLotsOfData, and *release* it in processLotsOfData once you're done with it. (Otherwise, you may find it deallocated from under you, something that wouldn't have happened before since the General/AutoreleasePool would have protected you.)

* Unfortunately, much of the General/AppKit cannot be called from within a worker thread, so we need to update the GUI from the main thread. Fortunately, General/NSObject provides a simple way of doing this:     -performSelectorOnMainThread:withObject:waitUntilDone:. Since, again, we've neatly bundled up the GUI magic, we can simply replace:

    [anotherObject presentDataToUser:processedDataObject];

with:

    [anotherObject performSelectorOnMainThread:@selector(presentDataToUser:)
                                withObject:processedDataObject
                             waitUntilDone:false];

Once again, avoid updating     processedDataObject once you've called this method, and be sure to retain and release in the correct places.

If you want to update the GUI incrementally during processing, so the user gets to see the first bits of information as the latter bits are being processed, this is as easy as adding more calls to the main thread.


As a final piece of advice, if you're thinking of using multiple worker threads, be sure to give them independent tasks to perform. Communication between worker threads is much more complex, and only rarely provides performance benefits - and that after much effort. Using multithreading simply to achieve a nice, responsive GUI is far easier and safer.

----
**Handling premature termination**:

If the user can cancel the processing, say by closing the GUI, you'll need to handle this gracefully. This has two parts: (1) terminating the worker threads early; (2) ensuring the     presentDataToUser: callback doesn't try to update a GUI that no longer exists.

(1) To terminate the worker threads early, simply provide a boolean     terminated flag that each worker occasionally polls. When the GUI is closed, or "cancel" is pressed, or whatever, flip the flag to true. Rapid response should be unnecessary, since the only cost to the user is a temporary period of non-lowered CPU usage, so using sporadic polling is not a problem.

(2) To thread-safe the callback, have it check the     terminated flag when it first starts, before it touches any objects. If the flag is not set, the callback is safe for its entire duration, since it is operating in the same thread as the code that handles the GUI being closed.

For best performance, you'll need to ensure the     terminated flag does not suffer from "false conflicts". This means putting it in its own cacheline, by padding it below and above with some untouched memory (64 bytes should do):

    struct { char padding0[64]; volatile bool flag; char padding1[64]; } terminated;

(Actually, provided you put it in a cacheline with other variables that are almost never updated, you should be fine. The problem comes when the cacheline is frequently updated; each worker thread, when it polls, will have to read the whole cacheline in afresh, even though the bit its interested in hasn't changed.)

**But beware**, the possibility of this construct breaking in possible future hardware has been raised below. In such a case, safety [i.e. (2)] is guaranteed, but worker threads may continue running until they terminate naturally. Using a volatile bool will also not scale to more complex tasks, such as allowing the terminating GUI thread to wait for all its workers to finish, or indeed **any communication between GUI thread and worker thread apart from as a termination flag**. If you want this extra functionality, or are seriously worried about some outrageous future platform dropping sensible cache coherency, you should investigate that friend to the multithreaded programmer, General/NSConditionLock. For this particular problem, for instance, the code you are after is:

    
terminationLock = General/[[NSConditionLock alloc] initWithCondition:NOT_TERMINATING];

// In the GUI thread, to signal for termination:
[terminationLock lock];
[terminationLock unlockWithCondition:TERMINATING];
// And to then wait for the worker thread to terminate (optional)
[terminationLock lockWhenCondition:NOT_TERMINATING];
[terminationLock unlock];

// And in the worker thread, call this periodically:
[terminationLock lock];
if([terminationLock condition] == TERMINATING) {
    // clean up any local state
    [terminationLock unlockWithCondition:NOT_TERMINATING];
    General/[NSThread exit];
}
[terminationLock unlock];


Using a condition lock may be less forgiving of too-frequent checking in the worker thread than the     volatile bool, and won't scale fantastically if you attempt to reuse it for multiple worker threads, but is guaranteed to work on any system where General/NSConditionLock works.

----
**Discussion:**

I've created this page in desperation after seeing all the "simple" homegrown packages to create worker threads. The above is four lines if your code is written with the separation between data processing and GUI intact - and that separation is probably a good one to have, anyway. It's simple to support incremental updating with it, unlike, say, General/ThreadWorker.

    +detachNewThreadSelector:toTarget:withObject: and     -performSelectorOnMainThread:withObject:waitUntilDone: avoid an awful lot of the problems that multithreading usually creates. Why are we recommending avoiding them?

Comments and corrections most welcome. The above code snippets have not been run through a parser :) -- General/KritTer

Following an email from a new General/CocoaDev user, I've updated the above to discuss terminating worker threads. Again, comments and corrections most welcome :) -- General/KritTer

----
Ensuring the correctness of a boolean flag like you suggest above is hard. At the very least, you must mark it     volatile and call a memory barrier after you write to it. There may be more complications than this. A much better policy would be to simply create an General/NSConditionLock, and have the worker thread check the lock's     condition at intervals. To set the flag, lock the lock, then     unlockWithCondition:. As a bonus, you can use this same lock to wait for the thread to terminate.

If your worker thread can block for an indeterminate period of time, then you can use a callback mechanism instead. Remove the second half of the main thread code, and then replace the worker thread setting of the lock condition to a     performSelectorOnMainThread: which notifies the main thread that it's finished.

This lets you put the burden of memory barriers and all that other stuff on General/NSConditionLock and you don't have to worry about it in your code. Subtle thread bugs are the worst, and so you should offload as much bad stuff to the provided libraries as possible. Locks Are Good For You. Embrace The Lock. -- General/MikeAsh

----
Actually, I think you've overcomplicated the situation. Normally, yes, you would need memory barriers, etc. However, let's go back to (1) and (2). For (1), you don't need memory barriers because the flag is not protecting any data. For (2), there's no multithreading issue at all because the flag is both set and read in the same thread. So there's no problem. (Trust me, I'm hoping to be a doctor soon ;) I would agree with marking the memory volatile, simply to avoid the compiler optimizing away the read in the worker thread. I've modified the code accordingly. -- General/KritTer

----
Is there anything that actually guarantees a timeframe for when the update to the flag will be made visible to the other thread, though? Even if current hardware does guarantee it, is there anything in the pthread spec that guarantees any particular update behavior for shared memory in the absence of thread primitives?

My suggestion may be overcomplicating things, but I know that it will always work on any hardware past, present, and future as long as the libraries keep functioning in the way that they say they do. It also allows you to add things like waiting for exit (as shown above) without much pain. Debugging these things is really hard, so it's important to be absolutely sure of correctness from the start, or at least be sure that if you did make a bug, it will be relatively obvious. -- General/MikeAsh

----
The way the coherency model works guarantees it. You're right that Pthreads doesn't, AFAIK. However, the only thing that will go wrong is, as I said, your worker threads taking a while to stop. Safety is absolutely guaranteed. The chances of x86 surviving the dropping of the coherency model is sufficiently small that (IMO) it's not worth worrying about the possibility of your "close window" behaviour clogging up the user's CPU on said hardware, since it won't be written in the same instruction set...

(To expand: the performance gain of dropping the "reads return a recent value" property of the coherency model is, basically, zero. What makes hardware more scalable is using something like NUMA sans coherency, which will require a complete rewrite of everything you've ever done anyway, since Pthread locks don't work. Or rather, they do work, but all they can actually guarantee is that only one thread can hold a lock at a time, whereas people use them to protect accesses to adjacent memory. Which won't work.)

Probably worth flagging up how problematic it is to do these kind of optimizations correctly, mind. Don't want users getting the wrong impression, and thinking one can get away without a General/PhD in concurrent algorithms ;) -- General/KritTer

----
Only talking about x86 is a bad idea. We already have another architecture (PPC) and the odds of getting yet another one sometime in the future are pretty decent. Think of all the people who wrote their code with embedded PPC assumptions like endianness, only to hit panic mode at WWDC05. It's much better to just write your code in a generalized known-to-work fashion the first time. Unless you're checking this flag way too often, there won't be any performance differences anyway. When you get to talking about putting the flag in its own cache line by embedding it in a ridiculously padded struct, that really sets off my alarm bells. -- General/MikeAsh

----
I wasn't talking about x86, actually, but rather every current processor and architecture specification. Even if we *did* move to an architecture where it was a too-much-CPU problem,     volatile would almost certainly be interpreted by the compiler as a command to synchronize the cache with main memory, since otherwise the keyword is meaningless. And, one last time: It's *really* ok, because it *can't* break. -- General/KritTer

----
    volatile is useful even in a single-threaded single-CPU environment, for things like     setjmp and     longjmp. -- General/MikeAsh

----
As far as I remember,     volatile is treated as a compiler hint by GCC. That is, GCC has no obligation to actually implement volatile semantics (as with the     register keyword.) Is this not correct?

----
I've added the General/NSConditionLock code, slightly modified, to the main text. Chiefly, I'm concerned that there is no guarantee that [lock condition] will be synchronized, meaning it suffers exactly the same problem as the volatile bool; I've made the code actually grab the lock to be sure. If anyone can find any documentation anywhere about this issue, I'll be happier. -- General/KritTer

----
The question of whether     -condition is synchronized is a good one. I couldn't find anything in the docs, so you are right to lock around the check.

I did a little searching, as this question interests me. I found two interesting threads on usenet (tinyurl linking through to Google Groups, be not afraid): http://tinyurl.com/yuqmaf and http://tinyurl.com/2ybcrj

Some choice quotes:

Near the beginning of the comp.programming.threads discussion:
    
In order for another thread to have access to the variable, its 
address must be taken, since it has automatic storage class. 

If another thread then modifies it through that pointer, if the 
program is to work correctly, it must be accessed with some 
mutual exclusion taken to ensure memory visibility.  The act 
of locking a mutex (among other things) will, on a correct 
POSIX implementation, flush any cached registers to memory.

...

Volatile can not guarantee thread safety.  It is also not required 
for thread safety.  Correct adherence by the application to the 
POSIX memory visibility rules is *all* that is required for thread 
safety.


And from the middle of the comp.lang.c++.moderated discussion:
    
I believe that 
the Intel caching is implemented with "write through"; that a write to a 
local cache will always generate a write access to main memory (perhaps 
with some delay), and that the caches of all of the processors will 
detect this, and flush their cached data.  From what I have been told, 
however, this is not true on IA-64; it is a feature of the IA-32. 


If this is all correct (a big if), then it means that there is no guarantee on data visibility and accessing any shared data without first using a lock is invalid. Applied here, it means that there is no guarantee the worker thread will ever see updates to the shared flag. What's more, if this last quote is true, then there is a real-world architecture (as much as IA-64 can be considered "real world"...) where this would actually happen.

So, is the above correct? If so, is it enough to use General/OSMemoryBarrier() after setting and before reading the flag, as long as you don't mind being specific to OS X? Should you just assume that you're only going to run on PPC and x86 and you know that their memory models work without any special calls? -- General/MikeAsh

----

It might be useful to think: am I writing software for multiple platforms, or just for OS X? If you're using Cocoa for this section of code, it's probably the latter, and if so, you definitely don't want to make the assumption that you're only going to run on PPC or x86 because Apple has demonstrated not just that they are willing and able to switch CPU architecture when the need arises, but also that it's the third-party developer's job to keep up.

Don't make the assumption that you know what hardware you want this to run on if there might be new hardware tomorrow. -- General/RobRix

----
IA-64 still maintains the illusion of linearizability. It just doesn't do write-back. The hardware benefits of dropping eventual-visibility are non-existent; the amount of code you break, staggering.

I would also strongly recommend not getting into memory barriers. If it's not safe to do something with reads and writes, it's best to use a lock, because memory barriers have different meanings on different platforms. -- General/KritTer

----
A fairly definitive article by an Apple engineer on threading and performance: http://ridiculousfish.com/blog/archives/2007/02/17/barrier/

----

Worker Thread - General/NSInvocation Part 2

http://nifty-box.com/blog/2006/12/worker-thread-nsinvocation-part-2.html

----
The     enqueue: method accesses     elements outside of the lock, that should be fixed.

----

Asynchronous Thread Messaging

http://www.2pi.dk/tech/cocoa/message_queue.html

----

I could have sworn I implemented General/AsynchronousMessages already. ;)
