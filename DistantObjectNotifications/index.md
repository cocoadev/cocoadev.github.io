---
layout: page
title: DistantObjectNotifications
---



Is it possible to have willChangeValueForKey / didChangeValueForKey (KVC) notifications for a remote object? For example, I need to have an animation in an NSTableView  row on the client, but I want to animate it from the server...

----

Why don't you implement the notifications the client-side, and just have whatever methods you implement make corresponding calls on your distant object (the server)?  It's not as automatic, but I think it would work fine.

