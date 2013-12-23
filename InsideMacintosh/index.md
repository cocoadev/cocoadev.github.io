---
layout: page
title: InsideMacintosh
---

InsideMacintosh is the name for a large library of books released by Apple describing the technologies used in the Mac OS.

In the 1980s there were just a few books (volumes) and they were numbered with Roman numerals; you'll sometimes see references to them abbreviated as something like "IM-IV" (InsideMacintosh Volume 4). By the 1990s so many books were coming out that they were named, and references were usually of the form "IM:Files", or if the name was particularly long just the acronym: "IM:IAC" for Inter-Application Communication.

In general most of these are now out-of-date with the advent of MacOSX, although certain books (or at least parts of them) are still relevant. You can retrieve most of the ones that aren't obsolete here:

 http://developer.apple.com/documentation/macos8/mac8.html

Books that may still be partially relevant for some Cocoa programmers, and why:


*IM:Macintosh Toolbox Essentials - http://developer.apple.com/documentation/mac/Toolbox/Toolbox-2.html
Finder interface (things like FInfo and DInfo still work today)
*IM:More Macintosh Toolbox - http://developer.apple.com/documentation/mac/MoreToolbox/MoreToolbox-2.html
Resource Manager, Component Manager (QuickTime uses components)
*IM:Operating System Utilities - http://developer.apple.com/documentation/mac/OSUtilities/OSUtilities-2.html
Gestalt, Date/Time/Measurement
*IM:Interapplication Communication - http://developer.apple.com/documentation/mac/IAC/IAC-2.html
AppleEvents, AppleScript
*IM:Files - http://developer.apple.com/documentation/mac/Files/Files-2.html
Aliases, FSSpecs, HFS (the original, not HFSPlus)
*IM:Imaging With QuickDraw - http://developer.apple.com/documentation/mac/QuickDraw/QuickDraw-2.html
QuickDraw (obviously!), PICT resources and files


Keep in mind when you're reading any of these that the information is usually very out of date. These books describe the concepts very well, but because they're so dated you shouldn't worry too much about the APIs and code they describe. There's almost always a newer and better API to use in MacOSX, with newer and better documentation. (The sample code is frequently Pascal anyway - heh.)

