---
layout: page
title: WindowShade
---

How would I create General/WindowShade functionality?

Would this be a good starting point?
"You install a global event monitor with the General/NSEvent class method addGlobalMonitorForEventsMatchingMask:handler:."

Or is there some way to rewrite the default minimize/maximize button to collapse a window to it's title bar?
Can I replace the default behavior of zoom //unzoom button at a system level?

End result desired is double-click title bar to collapse window to title bar only.
Any suggestions appreciated.

--
Unsanity.com has gone to considerable lengths to make this ("General/WindowShade") work, I'm sure you're familiar with it. They call their code "haxies" because they are sort of low-level code hacks, specifically I think tha one overrides the standard window class. If you want that I think you'd have to write your own window or view class; no small feat.
Chris
--
