---
layout: page
title: NSWindowForceFront
---

How do you force a window to stay in the foreground (i.e. Force Quit Window). I tried   [myWindow orderFrontRegardless] and this doesn't work.

----

Use a Panel instead of a Window and select the Utility Window checkbox in its info panel in InterfaceBuilder. -- Bo

Or just call [window setLevel:NSFloatingWindowLevel]; if you don't want the full range of NSPanel behaviors.

----

see also WindowAlwaysInFront

