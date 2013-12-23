---
layout: page
title: TextFieldClock
---

I want to put the system time into a text field and have it update automatically in real time.

----

Use General/NSTimer. If you want to update once a minute, make a repeating timer with an interval of one minute. If you want to update once a second, make a repeating timer with an interval of one second.

*The resolution of General/NSTimer is about .003 second*

----

I'm not the OP, and I'm not using Cocoa/Java, but could this be done using General/KeyValueObserving, without using a timer? I'm just getting into General/CocoaBindings and trying to improve my understanding of what it is and isn't capable of.

----

The thing to remember with KVO/Bindings is that you have to have something to observe - in the case of the system clock there isn't anything in say, General/NSProcessInfo to bind against that gives off the correct notifications.

Make an object with a single instance variable that's an General/NSDate (remember, that's all time is, really). Write a pair of accessors for it that are KVC compliant. Have an General/NSTimer set up with it that is a repeating timer to update on whatever interval you wish. When the timer fires, get the system time and use the accessor to set it. Somewhere else, you can bind to that object.

Value transformers, etc. are left as an exercise to the diligent student... :)

----

see also General/CountDownTimer
