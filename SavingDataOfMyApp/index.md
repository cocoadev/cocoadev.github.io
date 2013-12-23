---
layout: page
title: SavingDataOfMyApp
---

Hello.
I need to save some data of my cocoaApp in the system library (I think in the system library....).
I have done the app preference with a .plist file in library -> preferences and it works fine.
But now I have to save temporary files, or preview of images etc etc...
How can I do?
I have to create a folder in Library?
But I don't know the rules, if can I find some rules for this..
can you help me?
I hope you can understand my question... and sorry for my bad english..
----
I am not sure exactly what you mean by "the system library", but if you mean the /System/Library folder, I'd encourage you to find another way to do whatever it is you want to do... that folder is reserved by Apple for the core components of MacOS X, and you should probably never touch it.
If, on the other hand, you're wondering how to save stuff to the /Library folder, or to the current user's personal library folder (also known as the ~/Library folder, also known as the /Users/[username]/Library folder), then you should check out

*Apple's documentation on low-level file handling (http://developer.apple.com/documentation/Cocoa/Conceptual/LowLevelFileMgmt/index.html); and
*NSFileManager, which is the Cocoa way of creating folders (see http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSFileManager_Class/Reference/Reference.html)

--ToM

Where to Put Application Files:

http://developer.apple.com/documentation/MacOSX/Conceptual/BPFileSystem/Articles/WhereToPutFiles.html#//apple_ref/doc/uid/TP40001411-BAJHCHJI

