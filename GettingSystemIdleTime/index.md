---
layout: page
title: GettingSystemIdleTime
---

I'm trying to call a method or function only after the whole machine has been idle for X length of time (just like a screen saver would do).

So far I've only been able to detect when my own app is idle (using InstallEventLoopIdleTimer)
How can I get the event loop for the whole machine? (The outermost event loop, not the loop of any single application.)

In GetMainEventLoop "the main loop is the event loop for the main application thread" but I want to intercept events (or install an idle timer actually) that will be sensitive to ALL user events not just ones for my app!

I don't know if there even IS an "event loop" for the machine as a whole, but there has to be something parsing events and feeding them to the correct apps right? Wouldn't that be an event loop?

Otherwise what am i supposed to do? Install timers on EVERY event loop for EVERY current process?

How do I intercept all events from the OS BEFORE any apps get a hold of them?

OR if the OS has a "Main Event Loop" how do I retrieve that so I can use it to install an event timer or Idle Timer?

Sorry to seem so ignorant, I've worked my way backwards from the Cocoa NSTimer to the Carbon EventLoopIdleTimer but STILL don't know how to check if the WHOLE MACHINE is idle not just one application.

----

What are you trying to do? As far as I know there is no one mechanism for getting at global events, but there are bits and pieces out there. Knowing what you're trying to do will help us suggest the right solution.

----

Delving into Carbon, GetEventMonitorTarget allows you to intercept pretty much all events (see CarbonEvents.h for limitations). It only works if "Enable access for assistive devices" is enabled.

----

There are 3rd-party screensavers for OSX, so this has got to be possible. Not that I know how, mind you...

----

http://www.zathras.de/angelweb/sourcecode.htm#UKIdleTimer

"This is a wrapper around the Carbon EventLoopIdleTimer, intended to be used just like an NSTimer. An Idle Timer doesn't trigger until the user has been idle for a while, doing nothing in the GUI."

--MaksimRogov

*Carbon idle timers only pay attention to the app's event loop, though; when it says "doing nothing in the GUI", it really means "doing nothing in your app's GUI".*

----

Which, naturally, is worthless in terms of trying to determine if the user is idle (not just that he's doing nothing with MY app)!!!

Anyone know how I can see the source code for the ScreenSaverEngine to try to see how Apple manages this?

*uh... get a job at Apple?*

----

Sadly, getting idle time is one of the few things that was possible in the old Mac OS but not in Mac OS X, at least not publicly. Apple of course has private SPI to do this, and I believe it's exposed through the CoreGraphics system. Rumor has it that this will be made public in Tiger. Until then in theory you could do sketchy things like get the location of the mouse on a timer and intercept all keystrokes through an input method, but trust me, you really don't want to go that route!

----

I found some code on the Cocoa mailing list that should do the trick. Link:

http://www.cocoabuilder.com/archive/message/cocoa/2004/10/27/120354

It doesn't let you install an event handler, but it does let you get the system idle time. From there, it's trivial to trigger an action after a certain amount of idle time without polling, like so:
    
- (void)checkIdleTime {
    double idleTime = SystemIdleTime();
    double timeLeft = kTimeout - idleTime;
    if(timeLeft <= 0)
        [self doIdleMethod];
    else
        ...schedule timer to fire in timeLeft seconds...
}


----

I've wrapped the code that uses the IOKit approach into a Cocoa class that functions much like UKIdleTime. This way you can use the system idle time with delegates.
http://ryanhomer.com/blog/2007/05/31/detecting-when-your-cocoa-application-is-idle/

NOTE:  The above URL is no longer valid.

----

