---
layout: page
title: RetainingThreadSafety
---



Sharing objects safely between threads in a multithreaded app involves understanding exactly how to use RetainingAndReleasing and mutable/immutable objects, especially collections.

----
**Retain and Release**
----

If an object is shared between two or more threads, any of which may dispose of it, it becomes more important than ever to understand how and why RetainingAndReleasing works - look up the RetainCount and AutoReleasePool pages.

Basically, if a thread needs to call any method of an object, it **must** be sure to retain it first. This is a problem, because     -(id)retain is a method too, and so cannot safely be called without additional protection.


* Firstly, an object can be retained by the thread that creates it before being passed across. The second thread is then responsible for deleting it, but can safely call any of its methods in the meantime.

Note that it is **not** sufficient for the first thread to retain then autorelease the object. The first thread's AutoReleasePool is **not** the same as that of the second thread, and the pool may be emptied before the second thread even gets the object! Again, **retain** the object in the first thread then **release** it in the second. Do not use autorelease in the first thread.

* If the object is placed in common storage, an NSLock or similar mutual exclusion (mutex) system can be used to prevent the object being removed from this storage (and hence released) during the retain call. 

Again, if you protect your array or dictionary from change with a lock, be sure to hold that lock while you retain objects read from it. This will prevent those objects being freed at an awkward moment.

If using a reader-writer lock, this retaining need only be protected by a read-lock. RW locks are not directly supported by Cocoa, but can be partially simulated with an NSConditionLock. (The condition is the number of readers; write-locks must wait on condition 0. This does not however create a fair lock.)

* A mutual exclusion system could equally be used to protect the object itself, though the mutex used could not itself be released, making the system only useful when the object protected is very costly to maintain.



----
**Mutable/immutable objects**
----

The other issue of import is dealing with mutable objects versus immutable ones. Making a thread-safe mutable object is usually foolhardy - the cost is usually no less than using an external system, and often much greater, unless designed carefully.


* The simplest way of solving the problem is making modifications mutually exclusive - preventing two occurring simultaneously - hence "mutex". This is done by taking a lock before modifying the object. Of course, reads also cannot occur whilst writes do, so must also grab the mutex, but many simultaneous reads are possible. A lock capable of allowing multiple reads is called a reader-writer lock; Cocoa does not include one.

* Another route is to have an immutable copy of the object, and to clone it, mutate it, and replace the old object with the new. This is a very costly scheme, consuming lots of resources for very little gain, since by and large even dual-processor machines will not spend much time stalling a processor waiting for locks to free, while single-processor machines see no gains at all, only wasting time copying objects.

* Many more sophisticated designs exist for protecting mutable collections of objects against multithreaded modifications. If your design revolves around such a shared structure, and empirical testing has shown you that the cost of using a single big lock is noticeable, consider adopting one of these.

Hash tables are the easiest target, since the big lock is easily smashed into many small locks protecting each bucket. This kind of thread-safety is best coded into the collection object itself, so forget using Apple's code!

Also, recent developments (2002) in the field of lock-free algorithms have produced an efficient lock-free hash table. Alas, you'll need to be a dab hand with assembler to put one together, and without automatic garbage collection the schemes look less rosy. Check out Maged M. Michael's publications at http://www.research.ibm.com/people/m/michael/pubs.htm for a gander.


-- KritTer, who needed to get this out of his system ;)

