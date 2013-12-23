---
layout: page
title: PollingAJoystick
---

I'm interested in implementing a joystick interface to a simulation system I'm working on. This doesn't require game-quality latency or high performance (in fact, the math for the simulation is so heavy that by and large it tends to run at best at 10% real-time, which is OK since this isn't a game), rather, it'd just be nice to have a timer or thread that can get joystick positional values and store them, such that other code can query that data later on when it gets to it.

I've tentatively looked into the HID api and I've also looked into the SDL implementation. Both are *extremely* hairy, and frankly, I'm a little scared. For what it's worth, I'm comfy in C -- I'm just scared because using the HID to get joystick position seems like swatting a fly with a crane.

If some kind soul can either give me a 10000 foot followed by 1000 foot rundown of the HID system I'd be very grateful. I'm new to Mac OS X programming, having come from four years on linux and some of this stuff boggles the mind. I don't even know where to start. On linux I'd probably just open some file in /dev or /proc...

Otherwise, if anybody knows of a nice Cocoa class which wraps all this already... well, consider me 1 beer in debt to you (unless you're underage of course ;).

-- ShamylZakariya


----
You might want to check out www.idevgames.com, they might be able to point you in the right direction.

