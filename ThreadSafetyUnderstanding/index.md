---
layout: page
title: ThreadSafetyUnderstanding
---

I need to clear some things up about General/ThreadSafety. The documentation says General/NSUserDefaults is not thread safe... so if I were to use that in a thread, would I need to a) subclass it and implement an General/NSLock in the subclass, or b) make my own generic user defaults system that reads and writes to the normal user defaults before and after the thread is complete, or c) simply call performSelectorOnMainThread on the user defauts object? Also, with General/NSFileManager, what's the best way to make that thread safe? (threads are so annoying... ) :) Thanks --General/KevinWojniak

----

For the most part, General/ThreadSafety is only a concern when you have multiple threads writing to a data structure.  If many are reading you should always be ok (unless there is some strange side-effect code in what look like read-only methods).  If one is writing and many are reading, the sanity of what happens is fairly implementation dependent (it is usually fine unless you are working with data which must be modified atomically).

Most likely you should make your own classes which access General/NSUserDefaults and General/NSFileManager for you after locking a semaphore (an General/NSLock held as an instance variable) since subclassing classes which you didn't write is usually iffy (unless there is documentation telling you how).

You won't be able to use performSelectorOnMainThread since that call is one-way (as I understand it) so you won't be able to use anything which has a return value that you want (although it may be a possibility for the writing methods).

Since both of these objects are singletons you could cheat and lock access to them with static General/NSLock instances (although it is consistent with how those classes are accessed, it is generally an inelegant idea).

As I said before, though, you can usually get away with ignoring General/ThreadSafety issues so long as you are only reading data (but there is a possibility that the implementations will be put into an insane state by such behavior).  To be safe, though, the best way is probably to make these calls go through a thread-safe wrapper.

--General/JeffDisher

----

Wrapping calls to General/NSUserDefaults and General/NSFileManager is *not* enough to avoid thread safety issues. Cocoa can and does access these classes on its own, and it won't politely go through your wrappers first.

----

What may help out is the General/NSObject call 'performSelectorOnMainThread'. This will block your main thread, until your main thread can execute the selector. This may cause some slowdown of your main thread, but if your only doing it a few times, it should work.

-TSL

----

Well that does work if you're setting an object to General/NSUserDefaults, but doesn't help when accessing an object from it (    objectForKey).

----

There are a couple of solutions.

First, General/NSUserDefaults *is* marked as thread safe. I'm not sure where the OP got the impression that it is not. So "using the existing, thread-safe API" could be considered a solution.

Second, you can use an alternate API that is thread safe. In the case of General/NSFileManager, you could use Carbon or POSIX functions.

Third, you could go through the trouble of setting up a distributed objects connection between the threads, and then having a proxy for the General/NSFileManager singleton exist in the secondary threads that you could call. This is high overhead and not all that easy to set up, but easy to use.

----

No they're not. It says directly in the General/NSUserDefaults documentation that "User defaults are not thread safe."

----

That answers that question. Now I'm totally confused. The docs do indeed state as you've quoted. However, http://developer.apple.com/documentation/Cocoa/Conceptual/Multithreading/Tasks/foundation.html lists General/NSUserDefaults as being thread-safe. What gives?
