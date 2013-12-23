---
layout: page
title: IconPositionsOnDesktop
---

I looked through NSWorkspace and some other obvious ones, like NSFileManager, but no luck.  I can do this in (cr)applescript - is there no cocoa solution?

*The position of icons is managed by the Finder. If you want a Cocoa solution, I believe you'll have to send AppleEvents to Finder.*

**How to save a file w/o messing up its icon position?**

You're probably using writeToFile:atomically:, which rewrites the file, instead of modifying the file. In this case, you'd have to reset the icon position. -- KentSutherland

I'm interested to know how to do this, even in AppleScript... (i would prefer Cocoa of course) can you give me a snippet of code for an icon ?

----

Obtaining icon position is not currently possible with Cocoa (ie, there's no magic     -tellMeThePositionOfThisIcon:inThisFinderWindow: method). Double-clicking icons to open documents or launch applications is Finder's duty and Finder does not share its power, so to speak. There are no public hooks for developers to get such information. One approach that *may* be helpful is to see if AppleScript can tell you the position of **an icon** -- it'd be up to you to determine which one (the selected one?) and ask for its position. With Cocoa, you can get the results of this script and do whatever with it. Please understand - I know very little of AppleScript and I'm not sure if this is directly possible.

----

Icon position may be part of OS X's file system metadata. The Finder-invisible .DS_Store files may be the repositories for this sort of information. An internet search for this and related terms may well be the best way to pursue this inquiry.

