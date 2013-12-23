---
layout: page
title: UncaughtException
---

This is my first Cocoa application:
It is an General/AppleScript application

On build and run, the build is Successful and no errors or warning, but on run I get this error on te Run: page

    
2003-06-16 12:15:31.076 General/GalleryBuilder[523] An uncaught exception was raised
2003-06-16 12:15:31.103 General/GalleryBuilder[523] *** class error for 'General/ASKNibObjectInfo': class not loaded
2003-06-16 12:15:31.119 General/GalleryBuilder[523] *** Uncaught exception: <General/NSArchiverArchiveInconsistency> *** class error for 'General/ASKNibObjectInfo': class not loaded

General/GalleryBuilder has exited due to signal 5 (SIGTRAP).


I do not understand this and need some help please. 
Thanks,
Steven King
( mailto:steven.king@washingtonpost.com )

----
At a guess, I'd say you need to include the General/AppleScriptKit framework. You'll find it in /System/Library/Frameworks

----
Thanks, it worked, I thing when I started it was a Cocoa application instead of an Applescript Studio app, thanks again
