---
layout: page
title: FacelessAppUI
---

I'm writing a faceless application (using LSUIElement) and everything about the facelessness is just peachy, except that when I open a window (a preferences window, for instance), it remains hidden behind the windows of the frontmost "faced" application, even when I use makeKeyAndOrderFront. Does anyone know a way to get around this problem?

--SteveCook

You also need to invoke     [NSApp activateIgnoringOtherApps:YES]; *(Ah, perfect. Thanks a lot!)*
----
Another problem is how to deactivate your application after closing the preferences window.

If you invoke     [NSApp deactivate] then focus is stll on your application, but if a new application is launched, it will become active (perhaps a bug?) � as an alternative there is     [NSApp hide:sender], but it does more than deactive.

Last there is the possibility to query NSWorkspace for the active application before opening the preferences window and issue a launchApplication:appName after closing it, but it will be as clicking the dock, so it may bring up an 'untitled' window for document based applications.


----

see also:

ForceFrontmostApplication

ResignFrontmostApplication

