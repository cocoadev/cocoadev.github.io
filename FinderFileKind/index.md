---
layout: page
title: FinderFileKind
---



I'm writing an app that searches for some files and displays them in an NSTableView. I want to put in a column that shows the file's kind like the Finder's list view does (e.g. "Folder", "disk image", "StuffIt Archive", "JPEG Image", etc.), but I can't find any way to have the system give that string to me. I've tried NSWorkspace's getInfoForFile and something from NSFileManager, but all they do is return something not terribly useful: the file's extension, HFS type code, or the application that opens it. Is this going to have to be done with a Carbon call or an Applescript?

-- OwenYamauchi

----

I believe LSCopyKindStringForURL is what you want. --KevinWojniak

*In general, LaunchServices (which the above function is from) has a lot of useful Finder-type stuff that isn't found in NSWorkspace (or NSFileManager). Besides "kind strings," you can also find the default application for a file *type* (instead of a specific file), test if an application thinks it can open a file, and retrieve some more specific file info than what you'd get normally (like, is the file invisible in the Finder?). You can use LaunchServices by including the ApplicationServices framework, which also includes a few other Carbon-esque things that are shared with Cocoa, like CoreGraphics and PrintCore and stuff. --JediKnil*

