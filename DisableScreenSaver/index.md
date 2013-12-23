---
layout: page
title: DisableScreenSaver
---

Does anyone know how to prevent the screen saver from starting when a key application is not receiving any events (keys, mouse...)? I have an app for watching firewire video, and while it's running the screen saver does its thing after no keyboard/mouse input is received for the duration of the screen saver delay (as it should). Is there a system call I can make to reset the screen saver timer?

----

"Q: How can I prevent system sleep while my application is running?

A: Your application can prevent system sleep by calling UpdateSystemActivity once every 30 seconds. The following code shows how to create a CFRunLoopTimer that calls UpdateSystemActivity every 30 seconds."

http://developer.apple.com/qa/qa2004/qa1160.html

