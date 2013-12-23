---
layout: page
title: HowToKillAFrozenThread
---

Is there a way to kill a frozen thread from the main thread?

----

I don't think there is a way to KILL a thread, like you kill a process (I have looked for that too, but never found anything). If you want to kill it because it is locked and using 0%CPU, then, well, it is not using the CPU, so it's OK... If you want to kill it because it is in a infinite loop, then you have to use a BOOL variable that you can set from the main thread and that the other thread checks in the critical loop (which means you have to know where it might freeze). Sorry, not super helpful!  --CharlesParnot

----

Killing threads is called cancellation. Thread cancellation is a tricky subject. You're most likely referring to asynchronous cancellation, where the thread to be cancelled does not have to explicitly test for cancellation, which is really tough to implement, and IIRC is not supported on Mac OS X.

What are you trying to do?

----

I'm designing a template for a self healing server. I'm going to use DO for IPC and worker threads for each request. This server template is going to be a foundation for custom application services. My main design goals is to move the messy details of handling broken and frozen connections into a connection manager class. This class will launch the server process if it is not running, see if the server is responding using distributed notifications, kill/relaunch unresponsive servers and notify all clients of any updated proxies.  

The server right now is a Cocoa command-line tool that uses an NSRunLoop (configured as a server run loop). Here is where I am stuck. Detaching NSThreads and using NSConnections is the straight forward thing to do. I can easily deal with connections that are not responding to requests using timeouts, but I need a way to cleanup frozen threads that are stuck in a loop. Having the worker thread check a BOOL variable is an option, but I would like to design a catch all healing method (yes make it idiot proof). Plus, there is no guarantee that the thread will get stuck in my code. 

I'm thinking about having the parent process spawn a child process for each connection (one connection per application) and have these child processes detach worker threads for each request. This would isolate server connections to applications, so if one child process crashes, the server will still be able to keep valid connections open to other applications. Plus I will be able to ask the server to create a new connection from the client side without having to relaunch the service. But all of this could be done with threads if there was a way to force a thread to exit from the main thread.     pthread_self() and     pthread_exit() seem like they can do the trick, but I feel like I have to hack my way around passing thread info from one thread to another (doesn't seem like a safe thing to do). 

----

As I thought you are indeed looking at asynchronous thread cancellation.

Basically, don't do it. It's tough, and it'll be completely impossible for what you're trying to do. If you can kill a thread at any moment, that means it's very easy for the thread to leave something in an inconsistent state, or not clean up things. If you kill a thread that's allocated memory, you have a leak. If you kill a thread that had acquired a mutex, you have deadlock. Because threads share resources, killing a thread will not clean up very much.

Using subprocesses is definitely the way to go. Killing a subprocess is simple and foolproof. The OS will clean up everything it was using. Also, using a subprocess will protect you from things like segfaults, where a thread will not.

----

In reply to the third post: yes, synchronous cancel (having cancellation points within a program) is supported by OSX in pthreads. You have to set the cancel state of the thread to "PTHREAD_CANCEL_ENABLE" and the cancel type to "PTHREAD_CANCEL_DEFERRED". Beweare however that there are many "cancellation points" within system library functions what will catch a pending request for a thread to cancel and allow the thread to cancel while in that function.

In reply to the previous thread: Thread cancellation isn't that tough, and it isn't completely impossible in this situation. One can install cleanup handlers that are executed when a thread is cancelled, so if you're in a loop that acquires a mutex, performs operations on some shared resources, and then releases the mutex, you can create a function to release the mutex, then, as soon as you acquire the mutex, push this function onto the cleanup handler stack with "pthread_cleanup_push()" and then when you want to release the mutex, pop this handler from the stack with "pthread_cleanup_pop()" - note that the second argument of this function specifies whether or not to execute the function upon popping it from the stack, so instead of doing "pthread_cleanup_pop(0); pthread_mutex_unlock(mtx);" you can just call "pthread_cleanup_pop(1)" to pop the function and release the mutex in one function. With this in place, if your thread is cancelled anywhere between pthread_cleanup_push and pthread_cleanup_pop, the cleanup handler will be called and the mutex will be released. The same applies to allocated memory, everytime you allocate some memory, just push a cleanup handler to free that block of memory. Make sure you have a corresponding pop to every pthread_cleanup_push, as they are thought of as '{' and '}' respectively (and sometimes they are macros containing these braces).

Sure subprocesses are "easier", but they also introduce ALOT of overhead, and scheduling is alot harder, and don't even talk about sharing resources...

----

Thanks to whoever posted the above. I originally posted this question a couple of months ago, but I still would like to use threads instead of tasks in my server framework. The general design will be very simple to change from worker objects residing in separate subprocesses to having worker objects reside in separate threads. 

It sounds like you have a solid understanding of pthreads. Before I read up on     pthread_cleanup_push(), I have a couple of questions. 


*Can I create an NSZone for each thread, have each thread use their assigned zone for memory allocation and then have the cleanup handler only worry about destroying the zone?
*Have you ever used the system level call     malloc_destroy_zone()?
*You talk about having to keep track of mutual exclusions, but since I'm using NSConnections how am I supposed to keep track of locks created by these DO support objects? 


----

You cannot call out to external libraries if you are doing asynchronous kills of threads, period. You have no idea what those libraries are doing. What if malloc grabs a mutex, and you kill the thread before it can release it? Your entire program will deadlock instantly. What if NSConnection grabs a mutex? Dead. Without knowing exactly what kind of resources your libraries allocate (including things like allocating memory, opening file descriptors, acquiring mutexes, etc.), you cannot use them in an async-killable thread, period. Even if you're thinking, "well, I'll just write everything myself, then", keep in mind that this holds true even for libobjc. Unless you can be sure that an async kill happening in the middle of an objc_msgSend() will never do any damage (and I'm pretty sure there are locks in there to handle thread safety in the caching mechanism), then you can't use ObjC from an async-killable thread *at all*.

**Yep, there's a spinlock in Apple's message caching system. It's mutually assured destruction: kill it and die yourself.**

To throw myself onto the pile: killing threads is NOT cool.  All the issues mentioned above are completly legit: locks, leaks, and hosed data, as well as corrupted address spaces (you can mess up your page table) are insurmountable problems -- especially if you know a priori that the only reason you want to kill a thread is because something has gone wrong.  If you do it, please let me know which app you're releasing so I can make sure I don't use it.  ;)

----

OK, I'm convinced that killing off a frozen thread is a bad idea, so for me at least I'm not going to pursue this any further.

* if you know a priori that the only reason you want to kill a thread is because something has gone wrong.  If you do it, please let me know which app you're releasing so I can make sure I don't use it.*

Does this mean I should not terminate a frozen task? Is the idea of a self healing service a bad design goal? Is Apple taking the lame way out by allowing end users to relaunch the Finder?

----

Threads are not processes! Terminating a frozen thread is a horrible idea. It is not likely to make things better, and it is likely to make things worse, or simply kill your app altogether. Terminating a frozen subprocess has no such problems associated with it. I can only assume that statement was only referring to using this idea with threads, since there's no way it could possibly work with any degree of reliability.

----
* if you know a priori that the only reason you want to kill a thread is because something has gone wrong.  If you do it, please let me know which app you're releasing so I can make sure I don't use it.*

As the poster of that statement let me clarify.  I **DID** mean that with respect to threads only.  You can terminate a heavy weight process whenever you like.  There are obviously cases in which unexpected termination can be bad: eg, it can leave the file(s) it was writing to in an unpredictable state, or even cause a dead lock if other apps are stuck waiting for the terminated app to do something (.e.g send a message).  However, those are mostly higher level problems, and with journaled FSs the damage is limited to the data the process was manipulating, or second rate code that assumes no faults in the system.

*A minor correction; killing a process cannot corrupt your FS on any UNIX-alike. Journalled filesystems protect you from FS corruption in the event of an OS crash or power failure, not process death. Your file damage will never go beyond corrupting actual file data.***-- I agree with that... unless it's not a User process... but of course, that's what an OS crash is! :)**

