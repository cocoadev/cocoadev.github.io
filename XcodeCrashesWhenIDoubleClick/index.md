---
layout: page
title: XcodeCrashesWhenIDoubleClick
---

Whenever I double click _anything_ in Xcode v1.5 (e.g. source files under the groups and files, search results in a global find, build errors, even NIB files), Xcode crashes.  Any ideas on what to do to fix this problem???

I've noticed this problem first after installing the latest Quicktime 6.5.2 update.

Thanks,
Joe

----

Trash your  ~/Library/Preferences/com.apple.xcode.plist file and see if that helps.

----

Trashing the prefs did not help.  I am going to try to download and reinstall Xcode, I think...

----

Probably best. I've installed the QT update and can double-click to my heart's content.

----

I've re-"upgraded" the devtools, and it hasn't fixed my problem.  Will it cause any damage to uninstall dev-tools (using the included script) and then reinstall them?

Maybe someone has any ideas from this crash log:

    
Thread 0 Crashed:
0   com.apple.General/AppKit               	0x930b8eec -General/[NSOutlineView _userCanEditTableColumn:row:] + 0
1   com.apple.General/AppKit               	0x92fa320c -General/[NSTableView _userCanSelectAndEditTableColumn:row:] + 0x58
2   com.apple.General/AppKit               	0x92e7dbc4 -General/[NSTableView mouseDown:] + 0xc4c
3   com.apple.General/AppKit               	0x92f386dc -General/[NSOutlineView mouseDown:] + 0x2e0
4   ...ple.Xcode.General/DevToolsInterface 	0x994cd394 -General/[PBXExtendedOutlineView mouseDown:] + 0x2b4
5   com.apple.General/AppKit               	0x92e024d4 -General/[NSWindow sendEvent:] + 0x10e4
6   com.apple.General/AppKit               	0x92df4b98 -General/[NSApplication sendEvent:] + 0xebc
7   ...ple.Xcode.General/DevToolsInterface 	0x994869f8 -General/[PBXExtendedApplication sendEvent:] + 0x84
8   com.apple.General/AppKit               	0x92dfcfb0 -General/[NSApplication run] + 0x240
9   com.apple.General/AppKit               	0x92eb948c General/NSApplicationMain + 0x1d0
10  com.apple.Xcode                	0x0000a1d0 0x1000 + 0x91d0
11  dyld                           	0x8fe1a558 _dyld_start + 0x64


----

This is absolutely what to do. If you're going to try fixing something by reinstalling it, it's always best to remove it and do a *full* reinstallation, not an upgrade.


----

**Have you tried repairing permissions?**

Or doing and archive and install? Sounds more like General/AppKit has been corrupted.

----

I was able to fix the problem by re-installing the Mac OS X Combined Updater.  Thankfully it worked, because I am about 3,000 miles from my OS X 10.3 CD...

Thanks for all of the suggestions.

----
