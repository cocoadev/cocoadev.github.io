---
layout: page
title: NSInterfaceStyleForKey
---

For the time being, here is the comment straight out of NSInterfaceStyle.h.

// Responders can use this function to parameterize their drawing and behavior.  If the responder has specific defaults to control various aspects of its interface individually, the keys for those special settings can be passed in, otherwise pass nil to get the global setting.  The responder should always be passed, but in situations where a responder is not available, pass nil.

----
I have a feeling this function is now meaningless in Cocoa... probably still useful (?) on GNUstep...
----
Yep, it does nothing. You can set a global variable in a plist somewhere that determines the 'interface style' of the system. I believe there are strings for NextStep, Windows 95 and Mac OS in that header too. I forget which plist and key, but I experimented with this not too long ago on Rhapsody running via Parallels. It responded accordingly! However, this ability was removed when OS X was released. A shame really but look what happened with brushed metal and the pin striped windows.

