---
layout: page
title: NSIncrementExtraRefCount
---

    void NSIncrementExtraRefCount (id anObject)

*Increments the externally maintained extra reference count for     anObject. The first reference (typically done in the     +alloc method) isn't maintained externally, so there's no need to call this function for that first reference.*

----

There are two things I was wondering while looking at reference counting in Cocoa.

*retain/release goes through a spin-lock even though no threads is spawned for the process,
*reference counts are kept in an external dictionary.

Can anyone give the rationale for these design decisions? Since the run-time cost is rather high compared to intrusive reference counting with a conditional lock (where the condition is wether or not an NSThread was created).

----

Frameworks can not assume that your code nor the operating system (that may invoke a callback in your application on such a thread) did not create another thread either directly or indirectly. A sound design decision. Remember, NSThread is just a wrapper for the underlying APIs that be.

Correct me if I am mistaken, but off the top of my head, I believe the hierarchy is as such.


*Foundation NSThread & Carbon Multiprocessing Services

*BSD/POSIX pthreads

*kernel threads (in the OS X case **mach threads**)




**Note**: You will not get a NSWillBecomeMultiThreadedNotification notification when API's other than NSThread are used.

- NeilVanNote

- *I was under the impression that one had to use NSThread in a Cocoa application exactly to ensure that the notification was sent -- if a library uses POSIX to spawn threads, most likely that library would not interact with ObjectiveCee objects, and thus it would not matter.*

- *Such an API, given a callback into your code, could interact with Objective-C/C++ objects indirectly. The IOKit.framework jumps to mind; If I remember correctly, this framework performs it's callbacks on external thread(s) that Foundation is unaware of and in one of my projects I do interact with Objective-C objects from such callbacks.*
----

Counts are kept in an external dictionary to save memory and to allow all objects to contain reference counts. The common case of a refcount of 1 is represented with no entry in the table, so all of those objects with a count of 1 have no entry, and this saves some memory. You can refcount anything, even if you haven't made room for an inline refcount variable.

That said, these are the reasons, but I don't necessarily agree with them. I think the memory savings issue is bogus. The compatibility one is slightly better.

When implementing your own objects, if you discover that refcounting is taking up too much time, you can of course override     -retain,     -release, and friends and do your own inline refcounting.

*Okay, I was also wondering about the memory savings myself, but figured, how often do you have a reference count of 1? if it happens and the number of objects is high (so that the 4 bytes saved actually means something), one could just use structs (and save extra 4 bytes due to lack of isa pointer), since these objects are anyway sort of private (hence the reference count of 1) -- also, the objects with a reference count above 1 will occupy more than 4 bytes, since they need a CFDictionary node -- but as you say yourself, you don't necessarily agree with the argumentation used, and thus have probably made thoughts along the same lines ;)*

*The part about being able to reference count anything might be more useful, except that Foundation does not make that clear (the wording above about "externally maintained" is from the OpenStep specifications, and it has been removed for Foundation.*

