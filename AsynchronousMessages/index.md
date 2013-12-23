---
layout: page
title: AsynchronousMessages
---

I (JonathanGrynspan) put together this code to allow any object to asynchronously perform a method. You use it similarly to how you use -[NSUndoManager prepareWithInvocationTarget:]:
    
anObject gt_asynchronously] downloadSomeHugeFile];
/* or */
[[anObject gt_asynchronouslyWithDelegate: self selector: @selector(gotPi:result:contextInfo:) contextInfo: NULL] calculatePiToPlaces: 50000];

In the latter case, the delegate method is called on a secondary thread. The method's result is passed to the delegate method wrapped in an [[NSValue object.

The code can be downloaded from http://www.ghosttiger.com/?p=130 -- it used to be posted here, but I hope to keep it updated in a central location.

----
Interesting implementation with a delegate which probably the more cocoa way to do things. I did the same but with a ThreadPool and what are affectively futures. Where you can send a long calculation or even a short one to a different process but receive it's result immediately. As soon as you pass another message to the result it blocks 'till the calculation is complete. 

Something I hadn't thought about was what would happen with a gui. I'll have to think about this.

I think the reason I would keep it this way and not add delegates would be because of the way delegates would usually be used. I think delegates would tend to be used to inform the gui of the point at which the computation has reached. 

Also don't you need to tell your invocation which you are passing to the thread to retain it's arguments.

Stefan
----

The delegate here is not the same as your usual delegate object. Rather, it's the same as NSAlert's modalDelegate or similar callback-style delegates. As for NSInvocation: it will retain things as it needs to.

----
Although this will not have an effect as of Obj-C 2.0. The documentation says that an NSInvocation does not retain it's arguments by default (see documentation on -(void) retainArguments). Therefore I would have thought it would be necessary to call this.
 
----
Indeed, NSInvocation will *not* retain things as it needs to. This is a performance optimization, and if you plan on keeping the NSInvocation beyond the current method invocation then you need to manually tell it to retain its arguments.

What does Obj-C 2.0 have to do with this?

----
Whoops! I've updated the code to use -retainArguments. I'd overlooked that aspect of NSInvocation.
----
RE: What does Obj-C 2.0 have to do with this?
The problem doesn't exist for Obj-C 2.0 because it has GC. That's what it has to do with it.

----
Of course, if someone chooses not to use the GarbageCollector, or is unable to use it (targeting 10.4 or earlier) then the issue remains.

----
I have modified the code to accept a parameter for thread priority, so it can be set by the caller rather than the callee (useful when the callee doesn't know if it's on a worker thread or the main thread.) -JonathanGrynspan

----
I have modified the code again to allow for an analogue to -performOnMainThread:withObject:waitUntilDone:, similar to the concept behind PerformOnMainThreadTrampoline. -JonathanGrynspan

