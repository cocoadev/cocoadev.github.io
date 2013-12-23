---
layout: page
title: BrowsingPreferencesFiles
---



Mac OS X introduces a unified way applications can store preference data. You can browse your preferences in Terminal with the "defaults" command line program. Try "defaults domains" to get a list of preference domains. You can read and write to specific domains with the "defaults read" and "defaults write" commands. See "defaults" usage or the defaults man page for more information. See also WorkingWithUserDefaults.

-- MikeTrent

----
You can also browse your preferences with the plist editor that comes with the developer tools.  It gives you a view much like that of the NetInfo Manager.  Makes viewing large prefs easier than with the defaults command.  Your preferences are stored in ~/Library/Preferences in plist form everytime the database is sync'ed with the filesystem.
-- ObjectiveMolz

