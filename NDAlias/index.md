---
layout: page
title: NDAlias
---

NDAlias is a class to access the Carbon Alias Manager from Cocoa.

Your application can use an NDAlias to refer to file system objects (that is, files, directories, and volumes) in a way that doesn't expect the file system object's path to be maintained. The user then can move or rename the file system object without your program losing track of it. This behaviour is not always desirable, for intance with library resources. But for file system objects like documents or user folders, it is what Mac OS users have come to expect.

NDAlias has the following features:

* converts to/from paths (NSString), urls (NSURL), FSRef, and FSSpec
* conforms to NSCoding
* can read and write alias files
* provides alias-related categories on NSOpenPanel, NSPathControl, NSString, and NSURL
* retain-release and garbage collection support
* 32 and 64 bit support


NDAlias is written by Nathan Day under a very liberal license; details at: http://homepage.mac.com/nathan_day/pages/source.xml

The code is also available at github at : http://github.com/nathanday/ndalias/tree/master

There is similar class named BDAlias.

