---
layout: page
title: LaunchServicesCache
---

I have added an NSServices to my Info.plist, but it doesn't show up in the services menu. I figured that perhaps it has to do with LS having cached an older path to my app?

I have tried to delete various LSCache files in ~/Library, /Library/Caches and similar, but still "open -a <myapp>" brings up a very old version of my app (located in the build directory of a backup of the source).

I have tried to copy the latest version of my application to /Applications, delete the various LSCaches i could find and reboot -- but it still loads the old version.

So what is the procedure to flush the launch services cache?

