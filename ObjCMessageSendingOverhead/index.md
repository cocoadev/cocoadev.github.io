---
layout: page
title: ObjCMessageSendingOverhead
---

I was wondering how much overhead is associated with sending an ObjC message. I estimate the message is sent about 10^5 times in a process that takes about 30 seconds to complete. I reimplemented the method as a C function, and the messages as C function calls, and did not see any measurable difference in performance, relative to the 30 seconds, that is.

Noted in passing: In transforming the method to C, it was necessary to go from a method that has four arguments to a function
that passes eleven arguments, eight of which must be pointers insofar as they are instance variables of the class whose values must be preserved in between invocations of the function. Probably not relevant, but the code of the function dereferences the pointers extensively.

Is the approximately equal performance what I should have expected? How much does this depend on other implementation details?
How much work is involved in the tradeoff of de-referencing all those pointers? Can any conclusions be drawn? Is this even interesting?  ; )
----
The answers to all of your questions may be had by using /Developer/Applications/Performance\ Tools/CHUD/Shark.app. If you don't have it, install CHUD from your dev tools CD, or download from http://developer.apple.com/tools/performance/

----
more technical: http://www.mulle-kybernetik.com/artikel/Optimization/opti-3.html Of interest for your question: *The minimum overhead of objc_msgSend is therefore at least 30 instructions per call, sometimes a little more* (simple C calls look like 5 instructions if no inlining). Bottom line: 100000 messages take a ridiculously small amount of time as compared to 30 seconds (if your computer is less than 10 years old ;-)    Have a fun read! --CharlesParnot

----
I seem to recall it being said at the WWDC intro to cocoa workshop that the overhead was roughly 2-3 times that of a straight C function call i.e. for almost all intents and purposes it's negligible

----

Dee-lighted with these leads. Thank you, CharlesParnot, for the mulle-kybernetik URL. Even a quick read-through gives me much to ponder. I will have to re-read the instructions about caching and using IMP function pointers.

I take it CHUD is a performance/profiler suite (among other things). I have downloaded the new version.

----

You can almost always assume that the actual logic of your code is going to take lots of more time that the overhead of OO and those things. Shark is a good way of realising that. --TheoHultberg/Iconara

