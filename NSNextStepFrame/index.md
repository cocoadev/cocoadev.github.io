---
layout: page
title: NSNextStepFrame
---

What is an NSNextStepFrame?? Is it a subclass of NSWindow? It acts like one, but sorta. I'm very confused by it.

�I've never heard of it myself. It's probably private. For specific but small questions like this, you might want to try asking on Apple's cocoa-dev list, or the mamasam one, or maybe an IRC channel (irc://irc.freenode.net/macdev).

I would, but that draws from the cocoadev community of knowledge. I'm sure someone, three years down the track might want to know the same question. :)

----
It is indeed private and is a subclass of NSView. You can obtain it by getting the superview of a window's contentView. I don't know much about what it does but it appears to be a window's main view controller for window drawing tasks (such as the titlebar, toolbar, etc.) Use class-dump on AppKit to learn more.
----
Is this a real problem, or are you just curious?  That's a private class, and you shouldn't need to change behavior based on its details (and you certainly shouldn't rely on its details or existence).  Anyway, it's used with borderless windows.
----
Since Apple uses it (for the lock button in Safari's title bar), I believe that at the very least a superview of a window's content view, regardless of its class, is going to stay with us for a long while. This is the rationale behind some parts of my Afloat mouse-over code. -- EmanueleVulcano aka millenomi

