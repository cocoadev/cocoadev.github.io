---
layout: page
title: NSNotification
---

http://developer.apple.com/documentation/Cocoa/Reference/Foundation/ObjC_classic/Classes/NSNotification.html
----

NSNotification objects encapsulate information so that it can be broadcast to other objects by an NSNotificationCenter object. An NSNotification object (referred to as a notification) contains a name, an object, and an optional dictionary. The name is a tag identifying the notification. The object is any object that the poster of the notification wants to send to observers of that notification (typically, it is the object that posted the notification). The dictionary stores other related objects, if any. NSNotification objects are immutable objects.


Basically - you are handed these objects when you receive a notification - the original poster of the notification can put objects into an NSDictionary , you can get this dictionary  using the userInfo: method.

