---
layout: page
title: NSDistributedNotificationCenter
---

http://developer.apple.com/documentation/Cocoa/Reference/Foundation/ObjC_classic/Classes/NSDistributedNotifctnCtr.html

For sending and receiving notifications between processes (i.e. apps) - not between threads within one process. http://goo.gl/OeSCu

----


On Mac OS X v10.3 (don't know about earlier versions),     @"AppleDatePreferencesChangedNotification",     @"AppleTimePreferencesChangedNotification" and     @"AppleLanguagePreferencesChangedNotification" are sent to the default distributed notification center when these preferences are changed by System Preferences. This is undocumented, though, so use at your own
risk.

----


**Notes:**

Chris Kane from Apple shares some insight into the inner workings of NSDistributedNotificationCenter:

I've seen some email messages go by in the recent past that talked about 
using NSDistributedNotificationCenter to do messaging from one thread to 
another (usually main) thread in an application.  This isn't what 
NSDistributedNotificationCenter was intended for, but rather messaging 
between one task ("process") and another task.

Some seem to have assumed that distributed notifications are delivered 
to the main thread of a task.  This is not correct -- it is not defined 
which thread will receive a/the notification(s).  The implementation 
strategy currently is quite awful, and NSDistributedNotificationCenter 
just picks a thread to receive all the notifications.  It is possible 
that you might see notifications coming to the main thread in your app, 
make some changes to how you do things (with notifications), and 
suddenly find the notifications are coming in to another thread.

What we usually try to do in Cocoa is get "asynchronous 
callbacks/completions" back to the thread which started the operation.  
A notification is something like that, so it might be natural for a 
thread which registered to receive a notification to get the 
notification handler called in that thread.  In the future, the 
implementation may do this.  Or it may not.  Another valid strategy 
would be to make all threads which request notifications (add observers) 
become candidates for handling all/any notifications.  Or a single 
thread could be designated for all notifications.

The point is just that depending on behavior which is not defined (and 
it is often hard to know that you are even doing that) could be a tricky 
proposition if you're architecting an application around those 
assumptions.

(Based on a mailing list post by Chris Kane.)

----
OK, but it doesn't answer the question, why should I not use NSDistributedNotificationCenter for communicating between threads. While I need to _subscribe_ to receive a notification, I know exactly which thread can get it. Another question would be which thread can get the notification as the first and which is the next but it's behind this thread. My opinion is that for sending messages (notifications) from working thread to the main (GUI) thread is it ok.
-BobC

My understanding of Chris' post is you should not use it for communicating between threads because you cannot control which thread will receive the notification. So you might be communicating with the same thread, or with a different thread than you intended. In my opinion, creating a timer and adding it to the main runloop is a better way to do this kind of thing. This technique is illustrated in the open source project ThreadWorker. Check it out!
-- MikeTrent

My read of the docs is that you should not use NSDistributedNotificationCenter between threads because it is so expensive  - and not even guaranteed to succeed (if the system is under heavy load)
 --DiggoryLaycock

But also, as it clearly states, "The notification is usually delivered to the main thread�s run loop, but other threads could also receive the notification." This is totally useless for thread communication. -- KritTer

This is no longer the case, documented sometime before May 2006 (possibly long before, but I don't know for sure) and implemented in 10.3. To quote the docs:

----
For multithreaded applications running in Mac OS X v10.3 and later, distributed notifications are always delivered to the main thread. For multithreaded applications running in Mac OS X v10.2.8 and earlier, notifications are delivered to the thread that first used the distributed notifications API, which in most cases is the main thread.
----

So you can rely on which thread will receive the notification if you use 10.3 and up, and you can probably rely on it for 10.2 as well. It's still a bad idea to use them for the reasons DiggoryLaycock states. Since they can only be delivered to the main thread, there's no reason not to just use     performSelectorOnMainThread: instead. -- MikeAsh

Notification posted by a root process (unix daemon) using NSDistributedNotificationCenter is not received by user process. Is this a known behavior?
--PS

----
I that's intentional. Daemons generally run in the global Mach bootstrap namespace and cannot see user processes, which run in a different namespace.  You can get around it by using Unix domain sockets instead of notifications. I believe it's also possible to do it by moving the daemon to a less privileged account or forking off another process running in userland to handle communication with other applications.  (I'll be finding that out for sure in a couple days.)

----
Or use the     NSNotificationPostToAllSessions option.

----

One of the above comments states: "For multithreaded applications running in Mac OS X v10.3 and later, distributed notifications are always delivered to the main thread."

This this correct? Does this mean that the work around documented in: http://developer.apple.com/documentation/Cocoa/Conceptual/Notifications/Articles/Threading.html
is nolonger needed with 10.3 and greater?

