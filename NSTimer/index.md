---
layout: page
title: NSTimer
---

Apple's Class Documentation for NSTimer

https://developer.apple.com/library/mac/#documentation/Cocoa/Reference/Foundation/Classes/NSTimer_Class/Reference/NSTimer.html

See also UsingLotsOfTimers

----

NSTimer creates timer objects or, more simply, timers. A timer waits until a certain time interval has elapsed and then fires, sending a specified message to a specified object. For example, you could create an NSTimer that sends a message to a window, telling it to update itself after a certain time interval. Timers achieve this only if they are added to an NSRunLoop. the scheduled... methods create timers that are already added to the NSRunLoop. If you use the other methods, then you need to explicitly add them using the methods on NSRunLoop.

NSTimer instances can be set to repeat.

NSTimer is "toll-free bridged" with its CoreFoundation counterpart, CFRunLoopTimer.
----
A fired timer does not cause the AutoReleasePool to be emptied. Apparently it is only cleaned on user-generated events.
----
I'm a bit confused by the "invalidate" method of NSTimers. Does calling [timer invalidate] automatically call [timer release]? Does [timer release] automatically call [timer invalidate]? Do you have to call them in a certain order if you want a timer to be removed from the run loop and released from memory?
----
I'm not sure if the run loop retains the timer. If so, it will get released when removed.
That's not really important though, since you will have to retain and therefore release your own reference yourself anyway.
And, no, [timer release] will not automatically call [timer invalidate]. After all, someone else could still hold a valid reference to the same timer object.

To remove a timer, send invalidate first, then release.

An object reference might be invalid directly after sending the corresponding object a release message, so doing it the other way round you would possibly crash. -- AndreasMayer

----

Here are a couple of rules that might help you with NSTimer:

*A timer     retains the **target** and **userInfo** objects.
*A timer is automatically     retained by the run loop when scheduling it.
*If a timer is not set to repeat, it will automatically     invalidate itself upon firing.
*A timer is     released from the run loop when calling     invalidate.
*A timer     releases the **target** and **userInfo** objects when calling     invalidate.

In other words, if you release a repeating timer without invalidating it, it will continue to repeate because the run loop is retaining it. However, if you don't want to stop the timer before the application quits, or if the timer is non-repeating, you can release it after scheduling it without calling     invalidate.

----

Sometimes you can avoid using a timer and use performSelector:withObject:afterDelay: instead.

See http://www.cocoabuilder.com/archive/message/cocoa/2005/2/16/128373 for more info.

Just for clarification: if I use one of the class methods, scheduledTimerWithTimeInterval..., for a timer that will run for the duration of an application, I need not explicitly release it since it will be retained/released by the runloop, correct?

Correct; timers follow the same rules of MemoryManagement as everything else, so if you didn't alloc, copy, or retain then you must not release.

