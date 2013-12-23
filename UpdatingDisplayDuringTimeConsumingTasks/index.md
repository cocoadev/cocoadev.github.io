---
layout: page
title: UpdatingDisplayDuringTimeConsumingTasks
---

I created a Document-based cocoa application. And at a certain point the user has to start a time consuming task when a button is pressed. The cursor automatically changes into the spinning wheel indicating something is very busy. No problem until here. However if I switch to another application and back to my application, my document windows don't get displayed (only my inspector window) and if i press right-mouse button on my application icon in the Dock, it says "Application not responding" while the application is actually just busy computing stuff. How can I make sure my windows are displayed when switching applications? Because now, the user gets the impression my application just crashed.

----

I think you just asked the quintessential multi-threading question.

Time for you to start investigating General/MultiThreading. That page does not contain much useful information (HA!) but links you to the good stuff and Cocoa has a marvelous set of classes for helping you with this sort of thing. You should put that "time consuming task" in a separate thread so that your interface (which is running in your app's main thread) can go about its business. There are lots of fine examples of how to do this. Many times it is adequate to put your time consuming task into a separate method and then in your main thread call it by means of a selector with the General/NSThread class method

    detachNewThreadSelector: toTarget: withObject:.

The first argument of this method specifies the name of a selector in which you want to execute the "time consuming" code. The argument is specified this way:

    @selector( theOtherThread: )

and the declaration of that method might be

    - (void) theOtherThread: ( id ) anObject

where anObject is an auxiliary object you can use to supply additional data to your other thread (and you can pass nil there if you do not need to use it). Your thread selector does not have to specify an argument; in that case the selector argument will indicate this by not having a trailing colon character.

In the method     theOtherThread you need to allocate the thread's own General/AutoReleasePool at the beginning and release it at the end. Enclose all your time-consuming stuff between those events. If your General/MultiThreading is complex enough, and threads depend on each other, you may need to use other techniques to juggle all your threads so as to ensure General/ThreadSafety.  General/ThreadWorker is a class designed to help you do this when the job is simple.

Clear as mud? That's undoubtedly my fault. There are examples out there that illustrate in detail how to do this. Google away!

----

Note that depending on what your long-running task does, it may be possible to avoid (explicit) threads. For example, if you're loading the contents of a URL, you can use General/NSURLConnection's asynchronous callback mode instead of the synchronous mode. If you're waiting on IO from another process, return and wait for the various notifications that are provided. If you're doing a lot of processing in a loop, change the loop to a timer (General/NSTimer) which executes one iteration each time through. Of course sometimes the cure is worse than the disease, but threads are not the *only* way to deal with this problem.
