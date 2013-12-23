---
layout: page
title: NSWorkspaceNotifications
---



NSWorkspace has its own notification center that you can obtain by sending it the     notificationCenter message. It posts notifications for events like the launching of an application, mounting of a new device, or the waking of the machine from sleep. Consult the documentation for NSWorkspace

http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/ObjC_classic/Classes/NSWorkspace.html

for further details.

----

The notification center of NSWorkspace sends the NSW**'orkspaceDidMountNotification when the computer awakes from sleep. Is there a reason why this occurs? Do the mounted volumes unmount at sleep?

This is annoying because I have to add additional code to detect when the computer goes to sleep so it won't execute code on awake. Using NSWorkspace's NSW**'orkspaceDidWakeNotification notification is out of the question because it's for 10.3 or greater (I need Jaguar compatibility). Thanks --KevinWojniak

----

Or you could just keep a NSMutableSet of the volume paths you have handled.

----

To be notified when the machine wakes from sleep, register for the NSW**'orkspaceDidWakeNotification

----

My application needs to be informed about finder operations (create, copy, modify, delete, move etc.) in user-defined directories, and would need the full file path and type of change that occured. There are many ways, including kqueues or the FSEvents-API. Which one is the best to go, and is there also a way to use the NSW**'orkspace class?  --Reiner

----
Kqueues give you instantaneous and detailed notifications, but they require you to hold an open file handle to everything you want to watch, so they're only good for watching a small number (probably under 100) of locations at one time. They can't do recursive watching, so if you want to watch an entire directory tree, each directory in the tree counts separately.

FSEvents gives you somewhat delayed and vague notifications. It coalesces events together for performance reasons and it just tells you that something changed, and it's up to you to store the previous state, read the new state, and determine what changed (if you need to know). However, it will happily give you events for any directory tree or even an entire drive.

A third option would be to use FNSubscribe. This is a voluntary notification, where apps have to explicitly post that they've made a change. Finder *probably* does this. If it works, this will be fast but, just like FSEvents, will only tell you that something happened and it's up to you to re-scan. Because it's voluntary you risk not seeing changes, for example if the user manipulates things in Terminal, or uses an app that doesn't use FNNotify.

NSWorkspace can post the notifications needed for FNSubscribe (the     noteFileSystemChanged methods) but it cannot subscribe to them, so you would need to use Carbon directly for that.

