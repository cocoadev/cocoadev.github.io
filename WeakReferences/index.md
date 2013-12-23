---
layout: page
title: WeakReferences
---






Ken Case from General/TheOmniGroup uses the concept of General/WeakReferences to avoid certain problems when General/RetainingAndReleasing General/AnObject.

Here is a description in General/KritTer's words:

*If A strongly retains B, B cannot be deallocated before A is. It's quite easy to prevent resources ever being freed if all you have is strong retention. The memory may be lost in General/RetainCycles, unless you are in the future and using Apple's new-fangled garbage collection. However, even with a garbage collector, you may prevent resources being freed if you register them with a global object, like a dictionary.*

*If A weakly retains B, B cannot be deallocated without first notifying A to remove all references to B. This neatly solves the problem, leaving only the issue of implementation.*

*This is not a panacea for General/ThreadSafety. Even with a thread-safe implementation, one still needs to be careful to avoid General/DeadLock<nowiki/>s, and to be careful about how one follows weak references (if at all) to avoid data races.*

Here's a description in Ken's own words:

----

As some may have noticed in the General/OmniWeb beta 7 release notes, I've
recently introduced a notion of weak object references to General/OmniWeb
(actually, to our General/OmniFoundation framework).  I thought I'd explain a bit
about what those are and why we have them, for those who are curious.

Background:

One of the problems with retaining every object you have a pointer to is
that you build up a retain cycle, where A retains B and B retains A.  A
common approach to solving this problem is to carefully design your object
architecture so that A retains B, but B does not retain A because its
backpointer to A is guaranteed to be valid as long as it exists. When A
goes away, it nullifies B's backpointer to it (typically in -dealloc).  
An example of this is the observer pattern (as seen in notification
centers), where you add A as an observer of B and later remove A as an
observer in -dealloc.

Unfortunately, in the context of a massively multithreaded application
like General/OmniWeb, it's dangerous not to retain every object to which you have
a reference.  If you don't, thread 1 may be in the process of deallocating
object A while thread 2 is sending a message from B to A.  (The -dealloc
method in A has started firing in thread 1, but hasn't gotten far enough
to nullify B's backpointer to A before thread 2 starts sending its message
to A.)  Adding a lock in -[B nullifyA] or -[A dealloc] doesn't solve the
problem, because once you've committed to calling A's -dealloc method it's
already too late:  in thread 2, B could be sending a message to A which is
going to retain it again (perhaps scheduling another message in another
thread or something), but you're going to deallocate A in thread 1 no
matter what once you get past your lock.

So it seems like the best thing to do is to always retain every object you
have a pointer to--but as I said at the beginning, one of the problems
with this approach is that you create a retain cycle, i.e. if you make
object A an observer of B and B retains its observers, you can't wait for
-dealloc to remove A as an observer because -dealloc will never get
called.

Weak references:

My solution to this problem is what I call weak references:  you want to
be able to distinguish between strong references and weak (backpointer)
references so that when you have no outstanding strong references you can
invalidate all your weak references, letting you properly deallocate.

To that end, I've created the General/OFWeakRetain protocol, which has two
methods:  -incrementWeakRetainCount and -decrementWeakRetainCount. (For
convenience, one could also implement -weakRetain and -weakRelease by
calling -retain/-release and then the appropriate increment/decrement
method.)  Whenever we have a weak reference of object A, we increments A's
weak reference count; whenever we release a weak reference, we decrement
the count.  A's -release method checks to see if its current -retainCount
matches its current weak reference count, and if so it invalidates the
weak references to itself.

When object A is added as an observer of B, B now weakly retains A, i.e.
it retains A and increments A's weak reference count.  When A is removed
from the B's observer list, A's weak reference count is decremented.  
Whenever A's -release method is called, it checks to see whether all
remaining references are weak references (newRetainCount > 0 &&
newRetainCount == weakRetainCount) and if so it invalidates its weak
retains (removing itself from B's observer list), leading to the
thread-safe deallocation of A with no dangling messages.

For a concrete example of this in action, take a look at the our OIF
framework:  the images (class General/OIImage) weakly retain their observers,
which get notifications when the image learns its size, updates its bits,
etc., as the image downloads off the web.  When an observer is no longer
retained by anything other than the image it's observing, it invalidates
that weak retain by removing itself as an observer of that image.

If you'd like to see how an observer might implement this behavior, take a
look at the General/OIAnimationInstance class:  it's a subclass of General/OIImage which
sequentially represents various image frames in an animation (e.g., for
animated General/GIFs).  To do this, it observes the current animation frame's
image, forwarding the notifications for the current frame on to its own
(weakly retained) observers, so as an observer it implements the
General/OFWeakRetain protocol. It turns out there's a lot of common code used in
thread-safely maintaining the weak retain count, checking it against the
retain count during -release (but making sure that two threads don't both
check the retain count simultaneously, then both decrement it skipping
over the point where they're equal), etc., so General/OIAnimationInstance uses
some helper macros from General/OFWeakRetainConcreteImplementation.h which
implement the protocol.

My hope is that some of you might find the notion (and perhaps even our
implementation) of weak references useful.  I think it solves one of the
harder problems with retain counted memory management in multithreaded
applications.

*Edit: removed General/GarbageCollection rant; that should be on the General/GarbageCollection page.*

----
You might want to take a look at General/WeakPointers. Its been a while since I have visited the topic. I'd be
interested to know if Apple has addresses the problems with the Core Classes.

----

It's worth noting that this approach isn't totally encapsulated.  
If  

* General/SubclassB is a subclass of General/ClassA
* both classes use a weak retain count, and they don't share the same weak retain count
* obj is an instance of General/SubclassB with a positive General/SubclassB�weakRetainCount and General/ClassA-weakRetainCount


then obj will not be deallocated when only weak references to it remain.

So if Apple were to use this scheme privately, then Omni's code would break.  Which means that Apple *won't* use it privately, but it's just an example. 

-General/KenFerry
