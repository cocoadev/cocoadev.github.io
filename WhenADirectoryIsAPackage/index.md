---
layout: page
title: WhenADirectoryIsAPackage
---

Is there a fast way to determine if a file is a bundle or wrapper, or is this impossible to always predict?

----

Check out General/NSWorkspace's isFilePackageAtPath: method.

----

* Duh, thanks. I looked everywhere but General/NSWorkspace.*

----

I want to make a very simple application which counts recursively the size and the items of a folder. I don't want to count the elements that are inside a invisible directory or a package (like .pages, .app, .xcodeproj). The only way I have seen is to read all the application's "Info.plist" files and make a list of the package-extensions, but this is a very slow procedure. There are any methods, classes or functions that tell if a directory is a package or not?

General/NSWorkspace has a     - (BOOL)isFilePackageAtPath:(General/NSString *)fullPath; method.

Have a look here: http://trac.adiumx.com/browser/trunk/Other/General/XtrasCreator/General/NSFileManager%2BBundleBit.m  --General/DavidSmith  (It's BSD licensed, we just haven't gotten around to adding the license file yet)

----

Is there something insufficient about     -General/[NSWorkspace isFilePackageAtPath:]?

----

*Considering that the bundle bit is basically never used on OS X, I don't really get the point of that source code.*
