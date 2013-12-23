---
layout: page
title: MallocStackLogging
---

**MallocStackLogging**

This is an environment variable. When this environment variable is set, with "setenv MallocStackLogging 1" in tcsh (for example), then you can launch any app in that shell. Do not "open" the app, launch it from the shell. When you do this all mallocs are tracked. Then saying, in another shell, "leaks <name>" or "leaks <pid>" will give you a potentially voluminous list of the leaks in the app at that point.

You can then loop over some series of actions and see if the number of leaks changes between iterations. The leak has an id, which stays the same for the lifetime of the app, the size and sometimes a guess as to the type of the object being leaked, and a dump of the beginning bit of the leaked data.

Figuring how best to fix the leaks, and how to intepret some of the things you see in the leaks dump is a separate thing.

----

Note: I've had some crashes using MallocStackLogging inside my threaded application, when multiple threads are calling through to some stack logging APIs. From this, I believe that MallocStackLogging probably isn't thread safe (I only get the crashes if it is turned on) --MattBendiksen

----

I just switched from a PPC Mac to an Intel Mac, and my app dies with a SIGTRAP at a predictable spot if MallocStackLogging is set to 1.  If set to 0, no problem.  If run on PowerPC, no problems set to either 0 or 1.  Anyone have any suggestions?  -- StevenFrank

----

MallocStackLogging is horribly broken!  It makes malloc/free not-thread-safe.  Even if your app is single threaded, the OS sometimes creates other threads on your behalf, and if two of them happen to be in malloc/free at the same time, then you might crash.  This is with 10.4.x on both PPC and Intel.  See http://lists.apple.com/archives/mt-smp/2007/Jan/msg00010.html and rdar://4962019. This bug is fixed in 10.5. -- smcbride

----

I found and filed a bug against this. Bug ID #5083704. There is indeed a problem with MallocStackLogging* and multi-threading.  In short, the stack logging code has a lock to synchronize updates to the stack log. Occasionally when the stack log grows, the entire stack recording allocation must be moved to a new block in memory in order to accommodate the request (ie, realloc()). A race condition exists when this happens because the stack logging code keeps the lock within that allocated memory. By chance, another thread may need to record a stack allocation while a different thread is moving the stack recording allocation, and begin waiting for the lock at the original allocation address. However, once the thread that has the lock moves the stack recording allocation, the blocked thread will be waiting on a lock which will no longer be valid because it has been moved with the reallocation. The program will core dump at this point because the underlying memory for the old block has been unmapped from the process space. The top most frame in the stack trace will be a call to spin_lock(), which was called by stack_logging_log_stack().

My notes show that this bug goes all the way back to at least 10.2 and, at the time of writing this, to 10.4.9.  It probably goes back further, I just didn't check.  If you're interested in the source for the bug, as of Libc391.2.9, it's in gen/stack_logging.c inside stack_logging_log_stack() and begins around line 263, a call to spin_lock().

----

I can believe it's not safe, but I also use it extensively with a multi-threaded code base and never have problems. Well, the only problem I have is that if I let the application run for several hours, it crashes because it's out of logging memory. -- drd

