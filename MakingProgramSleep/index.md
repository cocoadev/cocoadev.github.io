---
layout: page
title: MakingProgramSleep
---



It seems that my program acts a bit too fast for the system to recognize the changes.
How would be the easiest way to make it sleep while waiting for the system?

----

[[NSRunLoop currentRunLoop] runUntilDate: [NSDate dateWithTimeIntervalSinceNow:1.0]];

----

Better, why don't you either register for a notification of whatever changes you initiated (if there is one), or failing that, poll. Yes polling is evil but sometimes there's no alternative.

----

Even if you poll, you shouldn't put it in a while loop. Instead, poll at intervals, using     [self performSelector:@selector(poll) withObject:nil afterDelay:some_small_number], or an NSTimer, so that your program can continue to be responsive while you wait.

