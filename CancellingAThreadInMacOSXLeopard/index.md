---
layout: page
title: CancellingAThreadInMacOSXLeopard
---

So, I'm playing around with threads. And in Leopard, threads are kind of revamped. You can instantiate a new thread and then start and cancel it. But:
How exactly does this cancelling work? I tried sending     -cancel to one instance of a thread I started with     -start and it didn't do anything - am I missing something?

----
From the Foundation release notes:

*The "cancel" state is advisory: this state can be polled by code to find out if it should stop what it is doing, but it does not force stoppage in any way. This is not the same as pthread cancellation. 'Cancelled' is a one-way state transition, you cannot set a operation object back to "uncancelled". The code being executed by the operation can terminate the execution by causing control flow to exit the -main method (by returning from that method or raising an exception from that method). Because the cancelled state is advisory, it is orthogonal to the "executing" and "finished" states.*

See http://developer.apple.com/releasenotes/Cocoa/Foundation.html

----

aka:      -start { while (![[NSThread currentThread] isCancelled]) { /* do some work ... */ } } 

----
And this while loop is in the thread-method, right?

