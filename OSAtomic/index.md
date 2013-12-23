---
layout: page
title: OSAtomic
---

OSAtomic.h is located at     /usr/include/libkern/OSAtomic.h. Despite the name of the enclosing directory, regular userland applications can use it by simply doing     #import <libkern/OSAtomic.h>.

OSAtomic.h provides wrappers for processor primitives such as test-and-set, compare-and-swap, and memory barriers. It also provides services built on top of these such as atomic increments, various other atomic arithmetic operations, and spinlocks.

By using these functions it is possible to build various data structures that are thread-safe but don't use locks. For example, a shared counter can be modified by using     OSAtomicIncrement32 and     OSAtomicDecrement32 rather than wrapping     counter++ with a lock. A thread-safe stack can be built using a linked list and     OSAtomicCompareAndSwap. This can result in a speed gain, particularly in cases where regular locks cause a great deal of contention on multiprocessor systems. It's also useful when doing realtime programming where potentially blocking a thread in order to acquire a lock is unacceptable.

Note that these functions should only be used if you really know what you're doing! If you thought regular multithreaded programming was hard, writing safe data structures using atomic operations is a hundred times harder. When in doubt, don't touch this file, and stick to the regular locking APIs. Consider that using OSAtomic instead of normal locks is generally PrematureOptimization, and one which is extremely likely to give you very weird intermittent bugs.

