---
layout: page
title: SendingAppleEventsToAuxiliaryExecutable
---

I have an auxiliary executable in my bundle which needs to receive AppleEvents.

The problem is how do I set the signature for this helper application (which is used to return the AppleEvents)? Normally the signature is taken from tne Info.plist, but that one belongs to the real application and not the helper.

----

So I have one AppKit application with bundle and everything, and I have a helper tool _inside_ this bundle. The helper has no extra info, since it needs no dock-icon etc.

This helper tool needs to send and receive apple events, so it needs an application signature for that (to receive them).

So basically how do I set this?

I wanted to use the NSAppleEventManager, but maybe I need to do it all manually instead.

----

*I wanted to use the NSAppleEventManager, but maybe I need to do it all manually instead.*

Seems likely.  Would be easier, probably, to upgrade your foundation tool to a faceless cocoa app.  The plist keys LSBackgroundOnly and LSUIElement are probably relevant.  Also: Why do you want to do this?  Maybe we can suggest an alternate approach.

*Like NSDistributedNotificationCenter*

----

Unfortunately the helper is not started by me but another application, and this application uses a key in Info.plist to locate the helper (which must be in Contents/MacOS) and it's this other application which also communicate with the helper using AppleScript.

----

I second the advice from the post above. NSDistributedNotificationCenter is probably with way to go since it's a one way communication. --zootbobbalu

