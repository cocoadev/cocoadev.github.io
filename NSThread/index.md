---
layout: page
title: NSThread
---



Apple documentation: http://developer.apple.com/documentation/Cocoa/Conceptual/Multithreading/index.html

----

NSThread is not as capable as Java's Thread class. It lacks:

*A built-in communication system.
*An equivalent of "join()".


Each thread has its own RunLoop and NotificationQueue. You will need to create an NSAutoreleasePool first thing, and free it when done.

When you schedule a timer, it gets scheduled on the current thread. Notifications can not cross thread boundaries. Threads should not be stopped externally, they should end themselves by reaching the end of the spun-off method.

ThreadCommunication can be handled by DistributedObjects or NSObject's **performSelectorOnMainThread:withObject:waitUntilDone:**, but the best way is by the libraries that other industrious people have written. Two good ones are InterThreadMessaging (which allows notifications and method invocations on arbitrary threads) and ThreadWorker (which executes a method on a separate thread and invokes a callback when done).

ThreadSynchronization can be handled well enough by NSRecursiveLock (i.e. the SynchronizedDirective), NSConditionLock, and NSLock.

In general, MacOSX is optimized for a ProducersAndConsumerModel. NSMutableArray and NSConditionLock work well for this purpose.

----

It seems that making a category - any category on NSThread is a very very bad idea. Specifically, Core Data falls in a complete heap even if an apparently harmless and unused category on NSThread exists.

----

Adding a category can't be the problem. You're doing something in your category (maybe overriding private methods by accident?) or somewhere else that kills Core Data. --OfriWolfus

