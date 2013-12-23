---
layout: page
title: NSTaskAlternatives
---



Since NSTask is not thread safe, what are the alternatives that are? Is popen( ) and the associated file functions thread safe?

----

Why does it need to be thread safe? Why would you ever need to access an NSTask from multiple threads? Usually you just launch the task and then possibly interact with it by using its stdin/stdout/stderr.

----

Well, I use NSTask to launch a Perl script which uses regex to process text via stdin and then outputs it via stdout, but it blocks the executing thread, so it has to be launched from a thread, but it's crashing on some of my customer's computers, so since NSTask is not thread safe I'm considering using popen(), unless I'm just misunderstanding something here...

----

You're misunderstanding what "thread safe" means. It does not mean, "you cannot use it in a thread". *All* code in your program is executing in a thread.

The fact that NSTask is not thread-safe means that you cannot use the same NSTask object from multiple threads without synchronization. You are probably not doing this, which means that your problem lies elsewhere. Nothing you have said so far has violated the non-thread-safeness of NSTask.

See the ThreadSafety page for more information.


----

A reason why one may want a thread-safe alternative to NSTask is when you want to launch a slow process in the background to avoid blocking the main thread, but allow the user to terminate the process when it's taking too long, e.g. through a Cancel button. Or you may want to terminate the background process yourself, e.g. when your application terminates. In both cases, the termination takes place from the main thread, because user interactions and NSApplication delegate methods are sent on the main thread.

