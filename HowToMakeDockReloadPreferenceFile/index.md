---
layout: page
title: HowToMakeDockReloadPreferenceFile
---

I just wanted to ask you guys something:

When you use the System Preferences Panel "Dock", and you set a new value for, say, a slider, the Dock.app doesn't have to be restarted but seems to receive a notification called com.apple.dock.prefchanged.

Now, when a third party developer writes an app for the dock, the app has to kill the dock process so that the dock restarts and loads in the pref file again - because the dock doesn't recognize com.apple.dock.prefchanged notification if it's not sent by the preferences-pane.

Is there a way to make the Dock recognize this notification? Because killing the dock is ugly, expose (and in leopard spaces) will be terminated as well which really isn't userfriendly...

Thank you very much!!

----

I, too, would be interested in a solution to this problem, for Dockyard. It's possible the change doesn't go through Dock preferences (i.e. NSUserDefaults and CFPreferences) but through some other medium, and the Dock would then save the change itself. I wrote a little test app to find out how this behaves, and sending the notification yourself doesn't work when you update NSUserDefaults with a persistent domain, nor when you write to the Dock's prefs file directly. BTW, killing the Dock also kills Dashboard and automatic desktop switching, in case anyone cares. What I'd like to avoid is the second or two it takes to restart. --JediKnil

----

Why not post a notification of the same name?

[[NSDistributedNotificationCenter defaultCenter] postNotificationName: @"com.apple.dock.prefchanged" object: @"probably the name of the changed pref" userInfo: /* probably */ nil];

----

I've tried this already. It didn't work (not with an object and userinfo, not without object and userinfo... tried all sorts of combinations). I even built a little NSDistributedNotificationCenter Application that logs all ongoing notifications and lets me post notifications... and it logged the notification of the dock prefpane - there was only the name @"com.apple.dock.prefschanged" ... no object, no userinfo... there's got to be a trick...

----

The trick is that the Dock prefpane doesn't use that notification to talk to the Dock; it uses the private CoreDock API in the HIServices framework. There do appear to be functions in the API for adding and removing items from the Dock (actually, maybe only removing), but I have yet to make them actually work.

----

Now this is interesting. However, it's a fact that the distributed notification is sent every time you change the preferences in the dock prefpane. Probably in combination with the CoreDock API, though. This turns out to be very promising. Please keep us posted on your process.

----

Any idea how to intercept the messages sent between HIServices and Dock.app?

