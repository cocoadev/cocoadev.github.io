---
layout: page
title: HowToChangeAProjectsName
---



How do you change the project name of an existing project? Simply changing the name in the project inspector does nothing.

 --zootbobbalu

----
Also change your Info.plist entries to the new name 

----

Do you mean InfoPlist.strings? When I change that I get an error because Xcode is looking for the wrong prefix header.

----

Close your project in Xcode, then rename your .xcodeproj file (or if using Xcode 2.0 or below, the .xcode file), then change the settings for your target(s). Clean, and recompile!

----

You could also use perl to munge through all the files and replace the project name.  Be sure to make a backup copy (or keep in a source code control system) first.

----

I've had success with "Rename XCode Project" at http://www.blackbirdblog.it/programmazione/progetti/27 . Just make sure you run it on a back-up. It's a free utility.

----

Doesn't seem to recognize the .xcodeproj format.

----

If you want to rename a project, you rename the project package in the Finder.

If you want to rename a target, select it in Groups & Files, and then use the File->Rename menu item in the main Xcode menu.

If you rename the .pch file in the Finder, you have to do so also in the Target Info window or in the Info.plist file itself.

If you rename the Info.plist file in the Finder, you have to do it in the Target Info window as well.

These names are pretty much independent of one another.

----

Hi, my name is Michele and I am the developer of "Rename XCode Project". I'm working on the 1.5 version with Xcode 2.x compatibility.

*No offense, but is an app really necessary? It's not *that* difficult to rename an XCode project. The first time I did it, I had a few hiccups, granted, but once I found everything, I had no trouble in the future.*

**No offense, but is bottled water really necessary? ;-)**

Why not ? :-)

----

Michele, is Rename XCode Project finished for 2.x yet? I'm looking forward to it.

----

Yes! i will publish it next week.
Available at now: http://www.blackbirdblog.it/programmazione/progetti/27

Michele, is an XCode plug-in possible, instead of a separate app? -WPostma


----

See http://arustisha.wordpress.com/2009/03/08/on-the-renaming-of-xcode-projects/ and note about how to do it in Xcode 3.2
----
Project -> Rename

