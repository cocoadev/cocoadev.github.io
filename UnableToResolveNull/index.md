---
layout: page
title: UnableToResolveNull
---

Has anyone seen the warning below when debugging? 
I think it has something to do with IB. (My IB is 2.4.2 (v364))

I noticed that IB was keeping references to old disused classes that no longer existed. There were no instances of these classes, so I deleted them. I think it was about that time that I started getting this error (But I can't check since I am too dumb to use SCM). So I checked through the Interface to make sure there were no references to these classes. So I'm flummoxed - especially as this only seems to happen while debugging and the message is so cryptic. And I'm getting loads of inexplicable EXC_BAD_ACCESS and unrepeatable errors since that time even though I am very confident of my memory management. Sounds like something that should be there, isn't. Hmmm

    
Loading program into debugger�
Copyright 2003 Free Software Foundation, Inc.
GDB is free software, covered by the GNU General Public License, and you are
welcome to change it and/or distribute copies of it under certain conditions.
Type "show copying" to see the conditions.
There is absolutely no warranty for GDB.  Type "show warranty" for details.
This GDB was configured as "powerpc-apple-darwin".
tty /dev/ttyp1
warning: Unable to resolve "(null)"; not loading.
warning: Unable to resolve "(null)"; not loading.
warning: Unable to resolve "(null)"; not loading.
warning: Unable to resolve "(null)"; not loading.
warning: Unable to resolve "(null)"; not loading.
warning: Unable to resolve "(null)"; not loading.
Program loaded.
(gdb) run
[Switching to process 4749 local thread 0xd03]
Running�
 

Any ideas?

----

With no further information, no, none. ;-) Can you tell us if your NIB loads at all or does your app crash before your interface even comes up?

----

The NIB loads, the application does not crash straight away but has strange behaviour generally, crashes at odd times when it accesses things loaded from the NIB (after - applicationDidFinishLoading). Am I right in that -applicationDidFinishLoading is only called after every object in the main NIB is loaded?

Then again this could just be something in gdb?

----

Well, as a general rule, assuming a bug in the compiler or debugger is an absolute last resort. It's not unheard of, but extremely unlikely. ;-) I'm afraid I have to say that if it's crashing at odd and random times, it usually means it's an autorelease pool thing. Pools usually get released during the idle time after an event is handled. I know you said you're very confident in your memory management, but the problem is about 5% likely to be the NIB file, since it loads. Something you're doing during startup (-applicationDidFinishLoading or didLoadNib or related) is very likely adding an object to the autorelease pool, then releasing it somewhere else in the code. After it finishes your routines and goes back to an idle state (waiting for some input), the pool is released and it's sending a release to a pointer that's no longer pointing to a valid object.

Every time I've had problems like this, it's always been the damnedest of memory problems. ;-) Of course the autorelease thing might not be it, but that's the very first thing I'd try looking for.

Now ... the process of elimination is your best friend. Start commenting out your own added subroutines (the order and process of how to do this can only be determined by you, since you've provided no code) and see what makes the problem go away. I've found that's the quickest, dirtiest way of finding a problem section of code.

----

Thanks for the response. I took out some code like you suggested which got rid of the EXC_BAD_ACCESS and SIGBUS problems, but still, these error messages - warning: Unable to resolve "(null)"; not loading. - come up (but only in the debugger, not when the program is running). So I'm not even sure now if they are related issues to memory management. I guess what I was really hoping for is that someone has seen this error message from gdb before, and has a clue what it is about. It doesn't tell you anything. Who is resolving what? And why is what set to null? 
Cheers

----

Okay, looking more closely, I see what you mean there. Since the errors are happening before the 'run', there might have been something screwed up with your General/XCode project. If anybody has any suggestions what to check, I'm sure we'd both appreciate it... ;-)
