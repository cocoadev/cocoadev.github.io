---
layout: page
title: RegisterForNotificationOfBackgroundApplicationClosing
---

I need to get a notification when a background application (in this case BOMArchiveHelper) closes, I tried using the NSWorkspace notificationCenter, however it does not seem to get events from applications that are currently closed. Is there any preferably simple way to accomplish this? Or alternatively is there a way to open finder created zips and decompress them while still retaining the resource fork.

----
Well, you could try using NSWorkspace's     openFile:withApplication: method to open the zip archive with BOMArchiveHelper from your application. --JediKnil
----
That is what I am currently doing, however I need to know when it finishes (hence the question ;-)).

*Oh. Maybe you could try launching it as a task, using the executable found inside the application. This seems to work for me, as long as you pass the zip file's path as an argument. (Admittedly, I've only tried this by hand from Terminal, but it should work inside an application). When the task ends, the archive should have been decompressed. --JediKnil*

Hey, thanks that worked :-) I forgot about that feature of OS X, been ages since i've used it!

