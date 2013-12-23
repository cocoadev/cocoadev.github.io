---
layout: page
title: CarbonEvents
---

CarbonEvents is a new Apple API designed to be more like other widely used UI APIs (read: Win32). Rather than polling the OS for events, the API user installs callbacks on interface objects, which are called by the OS when the time is right.

If we were to dichotomize the world of OSX APIs (and I think we may), Cocoa would be the good; CarbonEvents would be the bad; and Classic would be the ugly.

----

CarbonEvents is actually better than polling the OS for events, though :-)

-- FinlayDobbie

----

Is there still no Cocoa solution for this? (apart from polling?) 

--Wolfy

----

The Cocoa solution has always been to use the ResponderChain and NSEvent. You don't poll, you just implement the right methods, put your responder in the chain, and have the system forward you the right NSEvent to the right method.

Some of the methods you implement are -mouseDown:, -keyDown:, et cetera. This is probably explained very well in some of the CocoaBooks.

I recommend reading Apple's docs on NSResponder and NSView and NSEvent, it's some very cool stuff.

-- RobRix

