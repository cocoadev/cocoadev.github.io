---
layout: page
title: StatusOfHeadphoneJack
---



Is there a way (probably through Darwin trickery or with ioreg) to tell if a headphones is connected to the sound-out port?  I know that the internal speaker cuts out, and I'm told that on some earlier macs (orginal colored iMacs) this was through a hardware switch (indeed, when I plug something in, I think I can feel something push out of the way - maybe a switch).  But on my iBook (Dual USB G3), there almost seems to be a very slight delay before the internal speaker cuts off, leading me to believe that it's done through software.

*It's not a switch, it's just an interruption of the circuit going to the speakers. The pushing you're feeling is just a springy piece of metal for tension - to make good contact.*

What I'm thinking of is a utility that would allow you to set one system volume level (and perhaps app level as well) for the internal speakers, and another for the headphones, and automatically switch between them.

----

Actually I think the system already _tries_ to do that. I have a G5 General/PowerMac and the volume setting does change, unfortunately it's not very consistent, and sometimes it changes when a sample is played (e.g. I have headphones connected and am watching a movie, a sample plays, e.g. one of my notification scripts, and the volume changes to non-headphone level). It's quite frustrating ;)

Additionally if I sleep my machine with headphones connected, un-connect, and wake my machine, then programs start to hang (and can't be force killed) probably in relation to when they need audio out (i.e. it's Mail, Colloquy etc. which generally seems to hang, which have sounds) -- if either I sleep w/o the headphones connected, or remember to connect them before I wake the machine, there are no problems.

----

What I find super annoying is when I plug in my headphones to my Power Mac G5 the speakers stay on. Shouldn't they turn off automatically? Wouldn't that be the logical thing to do?

*They do for me...*

----

I believe this is very dependent on the hardware. For example, my iBook is able to play its startup sound through the external speakers; the Mac Mini isn't able to do that, however it mutes the internal speaker as soon as the system has started. However, the iBook is clearly able to determine which speakers are used (i.e. internal or headphone jack), while the Mini doesn't seem to be able to do that...
----

[OP] Hmm, I don't suppose there's a way to rungdb or some other Cocoa message-sampling program right around startup?  I suppose I could just run it when I plug in headphones to see if I could find the code, but I'd need to know what process is doing it (probably General/CoreServices, which would also be doing unp-teen thousand other thinsg.)  Or a way to trap all messages going to General/CoreAudio..

----
