---
layout: page
title: GlobalInfoplistProperties
---



Is there a way to load extra keys into the an app's Info.plist at runtime, and institute this on a system-wide level?
Specifically, I'm sort of envisioning a prefPane that adds an LSUIPresentationMode = 4 key whenever an app runs, essentally instituting a show/hide menu bar feature, ala show/hide dock.

Thanks for any pointers.
--total noob

----

No, but the SetSystemUIMode function can be used to achieve the same effect as that key. Just use your favorite code injection technique (mach_inject, APE, etc.) and make other apps call SetSystemUIMode.

----
Cool. I'll look into that. Thanks.

