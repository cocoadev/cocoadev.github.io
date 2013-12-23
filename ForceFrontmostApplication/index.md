---
layout: page
title: ForceFrontmostApplication
---

This is a really dumb question, but, how do I force my single window application to display as the frontmost application and get the event focus from within the application (Objective C)?

-k

----

In Cocoa:

    
[NSApp activateIgnoringOtherApps:YES];


I think this does what you need. If not you can do it with Carbon (see GetCurrentProcess and SetFrontProcess).

I'm not sure what you mean by getting the event focus.

-------------

If you do a SetFrontProcessWithOptions(&psn, kSetFrontProcessFrontWindowOnly);
to force only one window, it doesn't seem to work if NSBorderlessWindowMask is set for the window.
It brings two windows to the front instead! (Looks like a bug).

But if you set:
styleMask:NSTitledWindowMask|NSTexturedBackgroundWindowMask

It seems to look and work as you would like, and not have a title (despite NSTitledWindowMask being set).

