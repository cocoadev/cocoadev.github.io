---
layout: page
title: SomethingFasterThanWorkspacesIconForFile
---

Is there a faster way to get an application's/folder's/file's icon than     [[NSWorkspace sharedWorkspace] iconForFile:someFilePath]?

----

I agree that     iconForFile is slow.  In the past (when loading thousands of different icons) I've first used common dummy icons (for folder, document, etc) then refined the icons by building a list based on visibility that is fed to     iconForFile  running in separate thread. -- RbrtPntn

----

Yes,     [[NSWorkspace sharedWorkspace] iconForFile:someFilePath] is slow.

My solution was to implement an cache object for the icons. It was (over simplified description) just an NSDictionary with the key being the file path and the data being the NSImage. That way I only EVER fetched an icon for a file once. It was Ok for a quick and dirty optimization. 

You maybe able to get caching "for free" using [NSImage setName:] and [NSImage imageNamed:] but you'll have to try that yourself. 

----

This is much like a recent thread on cocoa-dev: http://www.cocoabuilder.com/archive/message/cocoa/2007/11/16/193207

----

[NSWorkspace iconForFile:] is going to be relatively slow compared to, say, [NSImage imageNamed:], because NSWorkspace has to do more than one disk hit to get to the image (check the file's resource fork, check the application bundle of the file's creator or of an application which claims it can open the file, check /System/Library/CoreServices for a default icon, etc.) However, the result is most likely cached, and NSWorkspace is one of the classes from the NeXTSTEP days and is really well-optimized for slow disk reads. Odds are, a slowdown is coming from your other code.

