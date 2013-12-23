---
layout: page
title: WindowPriority
---

I have a window that opens up in my application that I want the user to interact with. How do you make it so that user must close this window before he can do anything else? I believe they call it modal. 

----

If you want to make a window modal, then check out the     - (int)runModalForWindow:(General/NSWindow *)aWindow General/NSApplication method. However, this method will only stop the user from interacting with any other window in your application. It's not very OS X friendly to keep the user from interacting with other applications. -- General/RyanBates
----
Thanks Ryan, that will be good enough for my purposes.
