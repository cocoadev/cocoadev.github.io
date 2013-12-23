---
layout: page
title: StartingMyAppOnStartup
---

First, some terminology:



*startup item: these run before a user logs in
*login item: these run after a user logs in. Each user has a different set of login items.



The difference is very important.  This article is about login items.

Over the years, many developers have wanted their apps to run at login.  For a long time, there was no supported API to do this and various hacks were used.  As of this writing, the best-practice depends on which OS version your app is running under:



*For 10.5 or later use the APIs in LaunchServices/LSSharedFileList.h.  

*For 10.2 or later use Apple's LoginItemsAE sample code (<http://developer.apple.com/samplecode/LoginItemsAE/index.html>) or UliKusterer's Cocoa wrapper UKLoginItemRegistry (<http://www.zathras.de/angelweb/sourcecode.htm#UKLoginItemRegistry>)



Apple's documentation also details a great number of different ways to execute code at login and logout. Note that although this page doesn't say so, launchd user agents are completely broken on 10.4 and should be avoided:
<http://developer.apple.com/documentation/MacOSX/Conceptual/BPSystemStartup/Articles/CustomLogin.html>

If you want to programatically determine at runtime if your app was launched because it is a login item, see KnowingIfAppWasLaunchedFromLoginItems.

