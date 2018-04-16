---
layout: page
title: ScreenSaverEngine
---

ScreenSaverEngine is the Cocoa application that displays screen saver modules at the appropriate times (after so many minutes, when the cursor is in a hot-corner, etc.). It is part of the ScreenSaver system.

If necessary you can run the ScreenSaverEngine from Terminal:

    /System/Library/Frameworks/ScreenSaver.framework/Resources/ScreenSaverEngine.app/Contents/MacOS/ScreenSaverEngine

This is useful for running the engine under GDB for easy debugging.

The ScreenSaverEngine supports some special command line flags to aid in debugging:


*     -background -- All screen saver windows appear behind all other windows (behind Finder icons but in front of the desktop image). The password and gamma fade features are disabled. It will not exit on mouse or keyboard activity.
*     -debug -- Like     -background, except mouse movement over the desktop causes it to exit. Very handy when running the ScreenSaverEngine in GDB. (Trying to get around my computer in this mode without causing it to exit reminds me of playing Don't Touch The Floor as a kid)
*     -window -- Screen saver runs in default system window can be combined with -background flag, example: -window -background
*     -module <module name> -- Loads the specified module rather than the module specified by the user defaults.

*     <module name> accepts the same values as the     moduleName node in     ~/Library/Preferences/B<nowiki/>yHost/com.apple.screensaver.*.plist
* For example,     Flurry,     Abstract, or     Spectrum.
* It doesn't appear to accept full paths or URLs to     .saver,     .slideSaver, or     .qtz files, however, so it's necessary to place the desired module in one of the typical ScreenSaver folders and reference it without the filename extension.

High Sierra moved the file/folder location: 
(they also broke the -background flag, on high sierra) Still an active bug as of HighSierra 10.13.4
- it now requires putting computer to sleep or restarting computer to kill the process 
- because it runs in foremost window instead of background window.
    /System/Library/CoreServices/S<nowiki/>creenSaverEngine.app/Contents/M<nowiki/>acOS/S<nowiki/>creenSaverEngine


