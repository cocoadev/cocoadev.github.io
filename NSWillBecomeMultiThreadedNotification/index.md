---
layout: page
title: NSWillBecomeMultiThreadedNotification
---

**NSWillBecomeMultiThreadedNotification**

Posted when the first thread is detached from the current thread. NSThread posts this notification at most once�the first time a thread is detached using detachNewThreadSelector:toTarget:withObject:. Any subsequent invocations of detachNewThreadSelector:toTarget:withObject: do not post this notification. The observer methods invoked to receive this notification execute in the main thread, not the new thread, and they execute before the new thread begins executing.

This notification does not contain a notification object or a userInfo dictionary.
----
See: http://developer.apple.com/documentation/Cocoa/Reference/Foundation/ObjC_classic/Classes/NSThread.html

----

A clever use of this notification which Apple suggests is for creating thread-safe structures with no overhead while running single-threaded.

If you have a structure which has critical sections, Apple suggests that you put in the calls to a NSLock instance variable to protect the code.  The cleverness comes in when you initialize the NSLock instance variable to nil (thus, all calls to it are no-ops and only involve a runtime look-up as overhead).  Make the object register to receive that notification.  In the method that handles the notification, instantiate the NSLock.  Now the object instance is thread-safe.

Note that you may want to set some flag, or something, so that future instantiations of this object know that they need locks.

--JeffDisher

