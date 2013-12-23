---
layout: page
title: InterceptingNotificationsFromOtherApps
---



Is it possible to intercept notifications coming to and from other applications?  I think I saw some code somewhere on how to do this..  I'd like to be able to intercept the messages in iChat for when people come online and go offline and change status (I assume this is done through notifications)

----

When you say "to and from", do you mean distributed notifications (i.e. between apps?).  I don't think there's any reason to think that iChat sends distributed notifications when people come on or offline.  It makes sense that it sends notifications through its own defaultNotificationCenter, but that's internal to the app.

You can listen to all notifications going through the default distributed notification center by registering something like this:
    
[[NSDistributedNotificationCenter defaultCenter] addObserver:logger 
    selector:@selector(log:) 
    name:nil
    object:nil]

where logger is an instance of some reporting class.  I tried this just now, and I didn't see iChat sending any distributed notifications.

To listen to a notification sent through iChat's defaultNotificationCenter, you would need to be part of iChat itself.  That is, you'd have to load a bundle of your code into iChat.  This could be done with ApplicationEnhancer or with SIMBL.  From inside the app, it'd be the same process as always.
    
[[NSNotificationCenter defaultCenter] addObserver:observer 
    selector:@selector(action:) 
    name:<name> 
    object:<object>]

You can discover the appropriate name and object by logging all notifications, as above, and taking a look at what you see.


It may be that iChat sends apple events to its status menu item.  I think those might be catchable, but I don't know how.


----

As of Mac OS X 10.4, iChat Presence technology is available in InstantMessage.framework for getting information about iChat services and buddies, including notifications. In Apple docs, see "iChat Instant Message Framework Reference" and IMService. There's a code snippet in this public Leopard Technology page: http://developer.apple.com/leopard/overview/imframework.html . So, iChat Presence is in 10.4, and iChat Theatre is in Leopard. --PaulCollins

