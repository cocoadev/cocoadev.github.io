---
layout: page
title: WhatIsSIGTRAP
---

Hi,

I have been getting some Signal 5 SIGTRAP errors... and have been over and over the code and simply can't see what is creating them.

Can anybody give me ore info on what exactly a Signal 5 SIGTRAP error entails.. what should I be looking for to fix it? All I know is tat it's some kind of 'break-point' error, but I have no breakpoints set in my code (i don't think!?!)

Thanks for your input (I'm pulling my hair out)...
----
SIGTRAP is a Unix "signal". In my experience, if you're getting unexpected General/SIGTRAPs, it means you've tried to access a pointer that's already been freed.

In Cocoa terms, this means you've tried to send a message to an object that's been dealloc'ed. This usually occurs if you don't match up calls to the object's retain method with calls to its release method properly.

-tom

----

SIGTRAP is used by debuggers so that you can control the execution of your program if you don't have (or can't use) hardware breakpoints.

What do you mean by *SIGTRAP errors?*, though?  Is your programming terminating with signal 5, or something?  I didn't think that an unhandled signal 5 resulted in termination.

OP - Yeah the app was terminating with a Signal 5. I have tracked down the problem, and it was to do with retaining/releasing problems with my objects. Strange because I was expecting Signal 10 or 11 for those kind of errors.

----

See also: General/SignalsSentOnCrash
