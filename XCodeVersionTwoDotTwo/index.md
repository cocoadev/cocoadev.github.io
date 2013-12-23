---
layout: page
title: XCodeVersionTwoDotTwo
---



General/XCode 2.2 has been released:


*
Announcement 1: http://lists.apple.com/archives/Xcode-users/2005/Nov/msg00226.html
*
Announcement 2: http://lists.apple.com/archives/Xcode-users/2005/Nov/msg00227.html
*
Release Notes: http://developer.apple.com/tools/xcode/update.html (still on Xcode 2.1)



New features / Updates


* Includes GCC 4.0.1.
* Improved distributed builds, particularly in the areas of scheduling and error handling, and     xcodebuild now supports distributed builds.
* Target contextual menus items -- control-click on a target to build, clean, build and run, or build and debug the selected target(s).
* GCC now supports building executables with debugging symbols from the PCH physically separated from the Mach-O binary files.
* The linker now supports only linking in those symbols essential to debug a binary.
* Now you can globally turn off General/ZeroLink via the Build -> Allow General/ZeroLink menu item. When not checked, General/ZeroLink is turned off, overriding any settings in the project or target.


----

Just out of curiosity, does anyone know what Apple's process for updating gcc is? That is, how much work does it take for them to do a new gcc release? I ask because I've recently run into a minor compiler bug that's apparently been around since gcc 4.0, and apparently has been fixed in 4.0.3 or so, and I was wondering why Apple's release didn't bring us up to 4.0.3. I'm assuming it has to do with work on the General/ObjC compiler or some such, but I'm not really sure. -- General/RobRix

----

Xcode was a universal binary starting with version 2.1, so this is nothing new *deleting off list*

----

Any delta updates available?? 2.2.1 has just been released, but it's only available as a FULL download from what I can see. Downloading 800MB+ for a 0.0.1 updated is..... ridiculous!
