---
layout: page
title: AVSystemController
---

**AVSystemController**

    + (id)sharedAVSystemController;

    - (void)getActiveCategoryVolume:(float*)outVolume andName:(NSString**)outName;

Returns the volumn and name of the active category. Tells you whether headphones are plugged in.

**Notifications**

Sends a AVSystemController_HeadphoneJackIsConnectedDidChangeNotification notification when the user plus in or unplugs headphones.

Sends a AVSystemController_SystemVolumeDidChangeNotification notification when the system volume has changed (via up/down buttons, mute switch, or programatically)


Category:PointlessInformation‏‎

