---
layout: page
title: HowToDimTheDisplay
---

Hey guys,

I need to dim/turn off the display, just like pressing the display-dim button on the keyboard all the way until it's completely off. I've tried using CoreGraphics, but all that does is adjust the gamma, making the screen black, but not completely off. The difference isn't that noticeable, until the room is dark (which is the situation this will be used in); then, you can see that the display is actually emitting light.

Does anyone know how I can basically emulate the keyboard button that dims/turns off the display, or something like that? Thanks.

----
Pressing the dimmer key *doesn't* turn off the screen; if you're using a laptop in good direct sunlight you can see for yourself that it's still completely usable. Also, if you watch when the computer goes to sleep, there's also that moment of "black" before it is off. --JediKnil

----

If you really want to do power management, check out the files mentioned in this technote: http://developer.apple.com/technotes/tn2002/tn2075.html#TAN43

If you just want to fade the display to black, Quartz can do that.  See CGDisplayFade.

----

Oh, hey, you're right; it doesn't turn off the screen completely. Then, I guess what I need to do is put the display to sleep like when the computer has been idle for a set amount of time. However, I think this is different from "black"; when it's just black, the display is still technically on, so if you're in a dark room, you can see that it's still giving off some light (that's the problem with CGDisplayFade -- it just fades it to "black").

Would I have to use power management to put the display to sleep? I've taken a look at pmset, but from what I can see it only lets you set the amount of time before the display sleeps, so it wouldn't do it immediately.

----

Then please look at the link I posted above which tells you how to interface with the power management API.

----

Aha! I found out how to sleep the display: you use pmset, telling it to sleep the display in 107374183 minutes. It will sleep the display immediately.

The credit for this info goes to whoever runs http://linestreet.googlepages.com, with his Sleep Display widget and application.

----

That seems ugly.  Looking through the documentation listed in the technote,     /System/Library/Frameworks/IOKit.framework/Versions/A/Headers/pwr_mgt/IOPMLib.h has a few functions of interest:     IOPMS<nowiki/>leepSystem(io_connect_t) and     IOPMS<nowiki/>chedulePowerEvent(CFDateRef, CFStringRef, CFStringRef).  These seem like a much cleaner way to interface with the power management system from an application.

Edit: On second thought, it doesn't look like you can provide a constant to shut off the display...

----

Yeah, those don't seem to provide a way to sleep just the display; only the whole system. Anyway, after some googling, it looks like a lot of other apps/code use "big number solution"; they just don't tell anyone. :)

