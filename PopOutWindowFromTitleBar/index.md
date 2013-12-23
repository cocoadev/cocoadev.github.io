---
layout: page
title: PopOutWindowFromTitleBar
---

I am wondering if anyone has an idea of how to pop a window/panel out from the menu bar (The same way as in the app: LaunchBar). I cannot find a way to get a window above title bar, anyone know of a way to do this?

----

You need to create the window with NSBorderlessWindowMask and NSFloatingWindowLevel and position it just below the menu bar when it's opened.

See BorderlessWindow WindowAlwaysInFront and NSWindowLevel

