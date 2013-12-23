---
layout: page
title: ScreenSaverIdleTime
---

Has anyone had luck setting the ScreenSaver idle activation time outside of the SystemPreferences application -- and having the value change be correctly observed by the system?  The following bit of code makes the requisite changes in the ~/Library/Preferences/ByHost/com.apple.screensaver.xxxxx.plist file:

    
CFStringRef appID = CFSTR("com.apple.screensaver");
CFStringRef key = CFSTR("idleTime");

// Set up the preference.
CFPreferencesSetValue(key, 
                      [NSNumber numberWithInt:[foo intValue]], 
                      appID,
                      kCFPreferencesCurrentUser, 
                      kCFPreferencesCurrentHost);
    
// Write out the preference data.
CFPreferencesSynchronize(appID,
                         kCFPreferencesCurrentUser, 
                         kCFPreferencesCurrentHost);
    
// Let apps know this changed?
[[NSWorkspace sharedWorkspace] noteUserDefaultsChanged];


...however, the change doesn't always seem to take effect.  What does the Screen Saver preferences panel do to notify the system that this value has changed?

--MatthewSwann
----
I had the same problem when writing my menu extra, Dockyard. Dynamically loaded bundles just don't have a good way to access preferences. Anyway, the solution seems to be using the following code:
    
NSDictionary *screenSaverDefaults = [[NSUserDefaults standardUserDefaults] persistentDomainForName:@"com.apple.screensaver"];
*
...
// Change screenSaverDefaults somehow, probably through mutableCopy/setObject:forKey:
...
*
[[NSUserDefaults standardUserDefaults] setPersistentDomain:screenSaverDefaults forName:@"com.apple.screensaver"];
[[NSUserDefaults standardUserDefaults] synchronize];

The     synchronize method is what updates the actual plist files in ~/Library/Preferences, and presumably what sends out your notification. Hope this works! --JediKnil
----
Thanks for the tips!  The code I pasted above *does* make changes to the actual plist files... that part works great.  What I'm having trouble with is getting whatever process (the loginwindow?) that watches the idle time and kicks off the screensaver to notice that I've updated the file and changed the idle time.

The behavior I'm seeing is that the .plist changes 100% of the time, but the changes only take effect maybe 50% of the time.  Once it starts working, it seems to continue -- but it seems to be nondeterministic.  Since the Screen Saver pref pane is able to change this value and have it take effect with absolute certainty, I'm wondering if there's a message I need to send to loginwindow or something else I need to call in order for the changes to take effect.
----
You can send a notification to loginwindow to reload preferences with the following code:
    
           CFMessagePortRef port = CFMessagePortCreateRemote(NULL, CFSTR("com.apple.loginwindow.notify"));
           CFMessagePortSendRequest(port, 500, 0, 0, 0, 0, 0);
           CFRelease(port);


Preference changes will take immediate effect after sending that notification.

Thanks to posts by Guillaume O. and Jesse Hollington for figuring this out and sharing it.

*  - http://hintsforums.macworld.com/showthread.php?p=335913#post335913
*  - http://jesse.hollington.ca/post/3236821801/bluetooth-proximity-detection-on-os-x
 
--Noah

