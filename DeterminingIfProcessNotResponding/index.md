---
layout: page
title: DeterminingIfProcessNotResponding
---

I need some way of checking if a process on an XServe box is running correctly.
It's easy to check if the application has crashed and quit, as I can get cron to run an applescript every five minutes to check with system events as to whether it has that name in its list of processes and then deal with it if it's not found.

But I can't think of a means by which I can check if the app has just hung.


----

Try sending a basic Apple Event to the application, such as a version request or something. If you get a response, it most likely isn't hung.

