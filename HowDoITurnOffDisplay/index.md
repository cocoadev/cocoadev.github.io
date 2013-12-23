---
layout: page
title: HowDoITurnOffDisplay
---

Is there a way for an application to turn off / dim the display, and then turn it back on again later?

Thanks.

----

A quick hack to achieve this effect would be to create a full-screen borderless window at the topmost level (see NSWindowLevel) and fill it completely with a black rectangle.

A better way of doing it, however, would be to access the MacOSX API for controlling the display itself. It's called CGDirectDisplay and you can find out more about it here: http://developer.apple.com/technotes/tn/tn2007.html (the only disadvantage of CGDirectDisplay is that it's not Cocoa, it's straight C).

----

Thanks! I'll try that out.

