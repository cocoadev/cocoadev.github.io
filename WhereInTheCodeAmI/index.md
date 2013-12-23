---
layout: page
title: WhereInTheCodeAmI
---

You know how when you say      General/NSAssert(NO, @"Some message");  You get a pretty little dispplay of where in the code the exception was thrown?  You know, class, method, line number...  well, how can I General/NSLog something like that -- that's dynamically gotten out of the compiler -- and please, please don't say to handle a false assertion locally!  :)

----

The macros you want are     __FILE__ and     __LINE__, I believe. As in:

    General/NSLog(@"Blammo in %s at %s", __FILE__, __LINE__);

Worth a try, anyhow.

Note: there are two (2) underscore before and after     FILE and     LINE

----

Dude!  That is awesome!  Thanks, a lot.  BTW, you want to say     General/NSLog(@"Blammo in %s at %i", __FILE__, __LINE__); -- *__LINE__* apparantly returns an int (decimal constant) -- surprise, surprise.  Wow!  Is this doc'd somewhere!?  Thanks again!


**Section A 12.10 Predefined Names** of *The C Programming Language* also defines

    __DATE__,     __TIME__, and     __STDC__

*Good catch on the line number format. They're pretty common C preprocessor features; gcc's got documentation, and probably every other C compiler in existence does too.*
