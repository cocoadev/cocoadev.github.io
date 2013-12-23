---
layout: page
title: FullyTransparentWindowWithEvents
---

Is there a way to create a full screen window which has an alpha value of 0 (fully transparent) but which is opaque to mouse events?
I have thus far found that setting the window alpha below 0.06 or so causes mouse events to pass through the window.

If not, is there a way to install a desktop-wide mouse event filter, so that I can intercept these events before they are dispatched to other Apps?

-- ChrisG

----
For the former requirement,     [window setIgnoresMouseEvents:NO]. For the latter, CGEventTap.

----
That worked great, thanks.

