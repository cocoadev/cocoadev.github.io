---
layout: page
title: TrappingSystemLevelEvents
---

I just purchased Microsoft's Bluetooth keyboard and would like to write a driver so that I can use the utility buttons at the top (things like next/previous track, Calculator, and Messenger). When I press the buttons, however, they aren't passed through the cocoa responder chain. I was informed (through the bluetooth list hosted by Apple), that the events are passed on to, but not used by the System (the thread was specifically about this keyboard and Microsoft's companion mouse, and it was posted by an Apple bluetooth developer, so I believe the information to be true, and to pertain directly to this instance). Is there a way for me to trap and interpret these events? This is something I would like to try even I have to drop to the kernel extension level.

Any advice towards a solution would be greatly appreciated. Thanks --General/EliotSimcoe

There is a mention of intercepting keypresses in the kernel in General/KeyboardEvents. --General/AlexBrown

----

I had the same thought when I purchased my Bluetooth Keyboard from Microsoft, and after a lot of research, I found out from an Apple developer that the events aren't even sent to the kernel. They are lost somewhere in the driver. Unfortunately, this means that short of writing your own driver, it can't be done. -- General/EliotSimcoe
