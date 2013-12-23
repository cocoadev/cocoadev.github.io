---
layout: page
title: FunctionKeys
---

I have an basic Cocoa app in which I'd like to take over the function keys (F1-F12) whenever when my app is active.  For example, if my app is active when F1 is pressed I'd like to detect, then "eat" that event so that it is not processed by the system.

Any pointers would be much appreciated,
Brad S.

----

"Quartz event tap" is your friend.
Create an event tap to catch F1-F12 keydown event at system or session level. The callback function can return a NULL value to prevent this event to be sent to the next step of window server event processing.

MrBru.

