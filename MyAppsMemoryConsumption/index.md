---
layout: page
title: MyAppsMemoryConsumption
---

What's a good way for me to know, from inside my app, how much memory I'm consuming?  For example, if I want a unit tests that runs some piece of code a whole bunch of times and tests if there is a memory leak by checking if there is an unexpected rise in memory consumption inside the loop, how do I get that value?

----

"Leaks Appname" in the terminal?

----

He wants to know within his code, like in a unit test.

----
Yeah, I thought about doing an NSTask or something, but that is SOOO lame.  There has got to be a sys call somewhere for this kind of thing.

----

I believe sysctl will let you get the system memory usage stats just like you'd see in top.

