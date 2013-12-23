---
layout: page
title: NSConditionLock
---



http://developer.apple.com/documentation/Cocoa/Conceptual/Multithreading/

----**The definition**----

NSConditionLock is an implementation of the NSLocking protocol; it extends the protocol by allowing threads to wait on the lock until the condition is met for it to wake up. While a thread is waiting, it consumes no processor time, making this an effective means of putting a thread to sleep and waking it up again in a high-level fashion.

Each condition lock has an associated *condition* - an integer whose value can only be set on initialization (defaults to 0) or when releasing the lock (optional). Threads wait on the lock until the condition is a given value by attempting to acquire the lock (the condition aspect is, again, optional).

All this can easily be garnered from Apple's documentation, but what good is a condition lock?

----**Uses for condition locks**----

A condition lock can be used to synchronize the passing of information between threads, usually states. Drawing on ProducersAndConsumerModel, the states could be: 
* 0 - New data is not yet available on an interthread queue queue
* 1 - New data is now available on the queue

The consumer thread, when it finds it has emptied its queue, will simply wait for the condition lock to enter state 1 - indicating new data is available - and then rock the lock back to state 0 for next time. Producer threads, if they find they are adding items to an empty queue, will rock the associated condition lock to state 1 to indicate the queue is no longer empty.

Note that one must check carefully before entering a condition lock whether the thread wants to wait on the lock or merely grab it regardless. In this model, for instance, the consumer thread wants to wait on the condition lock, but the producers do not - it is irrelevant whether the lock already indicates new data or not, the condition will end up as 1 either way. In actual fact, making the producers wait on the lock in this case could cause the thread to hang for an unknown time if the consumer thread had not noticed the last time the lock was rocked to 1 (because it saw the new data on the queue first).

----

Another similar use for a condition lock would be a system where multiple threads are spawned to process many inputs from a small collection of identical sources and then deal with that input. The conditions would be: 
* 0 - At least one source is unwatched
* 1 - All sources being watched

The condition lock will double as protection for a shared section of memory about sources - which are watched and which not.

Each thread attempts to grab the condition lock in condition 0, so nearly all will block at once, leaving one holding the lock. Each thread, when it gets the lock, will find an unwatched source, mark it as watched, then release the lock, leaving it in condition 0 if there are still unwatched sources, rocking it to 1 if all are now watched.

When a watching thread gets something to deal with, it grabs the lock (ignoring the condition) and marks its source as unwatched again before rocking the lock to 0. It then goes off, does its processing, before repeating the cycle by waiting for the lock to reach condition 0.

A little analysis confirms this has the desired effect - every source will be watched by a thread if one is available, and the shared memory will be adequately protected from data races (see SynchronizationPoints) by the condition lock.

----**The hazards**----

Systems using NSConditionLock**'s have to be careful to avoid DeadLock. They are also susceptible to PriorityInversion, since they are by definition a means of blocking until a condition is true, but this is less relevant to most Cocoa programmers.

----**Example codes**----


* NSConditionLockMultiJobExample
* ProducersAndConsumerModel

