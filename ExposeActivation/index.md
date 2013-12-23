---
layout: page
title: ExposeActivation
---



I'm looking for a way to detect when expos� is activated so I can turn off some custom cursor updates that are happing on a MouseMoved Event. From what I can figure out expos� manages to move the windows without moving them with the window server (pretty sure core graphics has something to do with this). This causes them to "think" they are still in the same place and allows mouse moved events to filter down to them. Unfortunately this means that my custom cursors are still active and change when you enter and exit there respective regions with expos� active. I overrode NSApplication and printed all the events coming into sendEvent, but nothing is sent when expos� comes up, any ideas. --CurtisHoover

----

I have an idea: search before you post ;) - see DontExposeMe

*I doubt that'll help - the OP wants to see when expos� is activated(/deactivated), not necessarily to exclude a window from being expos�d, but to turn off/on custom cursor updates. Read before you post. ;)*

----

I've already looked around on this site and many others, and I havn't found anything about what I want to do. I need an event or notification. --CurtisHoover

----

Never used it before, but what about the NSWindow's notification windowDidExpose:? Sounds good to me!
--ZacWhite

----
Nope!

    Posted whenever a portion of a nonretained NSWindow is exposed, whether by being ordered in front of other windows or by other windows being removed from in front of it.
----

