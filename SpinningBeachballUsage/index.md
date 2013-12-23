---
layout: page
title: SpinningBeachballUsage
---

Hello,

I just spent a while looking online for official information about the use of the spinning beachball cursor in order to reply definitively to this: http://sophos.ca/pipermail/gnumail-users/2003-October/000285.html email, which I believe is wrong.

I *know* there's no public Cocoa API for setting the spinning beachball cursor, and I was completely sure that the HIG would tell me that 1) the user should never set it, 2) it means precisely that the app is not responding to events and 3) that an NSProgressIndicator should always be used instead.  But I can't find that info.  Am I wrong?  Can anybody either find the official word or at least tell me that I am/am not off my rocker?

-KenFerry

----

It's under **Feedback and Communication**:

*In Mac OS X, the kernel environment detects when your application doesn�t respond to events for 2 seconds and automatically displays a busy cursor.*

--AllanOdgaard

Thanks!

-KenFerry

Due to another post, I noticed that one can actually set a busy cursor using:
    
   SetAnimatedThemeCursor(kThemeSpinningCursor, n);

But this gives the black and white wheel -- perhaps this is what the original poster (linked above) was referring to.

----

That's a Carbon call, and displays the old black & white 'beachball' cursor. Nothing to do with this, really.

