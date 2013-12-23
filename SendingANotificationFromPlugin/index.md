---
layout: page
title: SendingANotificationFromPlugin
---



Hi,
After reading documentation I'm a bit confused about different types of notifications. I have an app with some plugins installed in its bundle (PlugIns directory) or in Applications Support (/myapp/Plug-Ins). It works fine, but what I need now is to send a notification from plugin and catch it in the main app itself. Can I use plain simple NSNotification for this     [NSNotificationCenter defaultCenter] postNotificationName:@"name" object:self]; or should I use NSDistributedNotificationCenter?

I did a test with NSNotification, and it  works, but I'm not sure if it's a correct way to do such things.
And if this makes any difference I'm also running a plugin in another thread, not in the main one.

----
What you want to do depends on what you want to happen. Once your plugin loads, there is no difference between it and the application, so your worry about sending a notification "from a plugin" doesn't make much sense. It makes no difference.

You may, however, have some problems with the fact that you're sending the notification from a secondary thread. Notifications are received on the thread where they're sent. Using the setup you have now, the main application must be aware that the notifications are being sent on a secondary thread, and refrain from doing anything (like twiddling the GUI) that is forbidden from such threads. If this is not the case, use     performSelectorOnMainThread: in your plugin to invoke a method on the main thread, then send the notification from there.

----
Thank you so much for explanation, the things are more clear now.

