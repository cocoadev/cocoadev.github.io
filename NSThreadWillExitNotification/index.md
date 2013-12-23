---
layout: page
title: NSThreadWillExitNotification
---

**NSThreadWillExitNotification**

NSThread posts this notification when it receives the exit message, before the thread exits. Observer methods invoked to receive this notification execute in the exiting thread, before it exits.

The notification object is the exiting NSThread. This notification does not contain a userInfo dictionary.
----
See: http://developer.apple.com/documentation/Cocoa/Reference/Foundation/ObjC_classic/Classes/NSThread.html

