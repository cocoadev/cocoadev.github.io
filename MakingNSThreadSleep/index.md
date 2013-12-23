---
layout: page
title: MakingNSThreadSleep
---

I recently poked around a bit with using NSThread instead of timers.

Now, I'm fairly comfortable with Qt's threading API ( not QuickTime, TrollTech's Qt GUI Toolkit ). Qt's threading API allowed one to force a specific thread to sleep for a number of seconds/milliseconds via a static QThread::msleep ( http://doc.trolltech.com/3.3/qthread.html#msleep )

This worked as expected. However, looking at NSThread's API, I couldn't find anything similar. The obvious method was      + (void) sleepUntilDate: (NSDate *) date  but the notes say that this blocks the run loop. That's unnaceptable.

A while back I extracted QThread and made it standalone ( e.g., not dependant on the rest of Qt ) and as such, I tried putting a call to QThread::msleep() from within my NSThread's loop. It worked as expected! But, it's also kind of crude. It also forces that class to use Objective-C++ -- which isn't a problem per se, considering 80% of my gui code is already Objective-C++, but I try to keep the C++ out when I can.

Now, I could just write a category for NSThread that invokes the pthread code that QThread::msleep() calls. But I'm wondering if I'm just missing something.

--ShamylZakariya

----

Perhaps     [[NSRunLoop currentRunLoop] runUntilDate:myDate]; ?  -- Bo

----

I think you may be a bit confused. When the docs say that     sleepUntilDate: blocks the runloop, it means that it blocks the runloop of the thread in which you call it. If you spawn a separate thread, and have it sleep using that method, then the main thread's runloop will *not* block. It looks to me like this is what you're doing, so you should be fine. -- MikeAsh

----

Ahhh. When I think "run loop" I think "the application's main run loop" not the thread's. But I guess with testing I could have experimentally determined it. Regardless, I already whipped up a category using pthread's sleep functions. If anybody wants it I could submit it to the SampleCode, however, it's derived from TrollTech's GPL'd sources -- so it's moot anyway if you're not writing open-source code.

--ShamylZakariya

----

Each thread can have its own runloop. It's not required, but it can be useful for things like distributed objects, or waiting on IO from multiple sources. I'm sure that     sleepUntilDate: does a small amount of calculation, then calls straight through to the pthread functions; NSThread is really just a thin layer on top of pthreads on OS X. -- MikeAsh

