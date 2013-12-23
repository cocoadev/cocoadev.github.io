---
layout: page
title: XcodeWindowPrefBug
---



I just had a problem with a project where the prefs for the debugger window was corrupted. The split view in the debugging window was permanently snapped to one side. The divider was also not visible so I couldn't do anything to make the left side of the main debuggin window visible (thread info and variable info views). Quiting Xcode and relaunching did not solve the problem. As a last resort I backed up the project and started to play around inside the     .xcode directory. I figured out how to force Xcode to rebuild a particular project's window placement prefs.

WARNING!!! Dont try this without backing up you project first!!!!!

What worked for me was to delete the     .mode1 file contained inside the     .xcode directory. As far as I can tell Xcode is able to open the project without this file and simple rebuild the file with default values. 

--zootbobbalu

----

update 11-23-04: 

This fix is not perfect. The debugging window now displays properly, but editor text view frames are not being saved now, so I guess I might have to create a new project to fix this problem. --zootbobbalu
