---
layout: page
title: NSTableWithMultirowNSCell
---

Is there any way to create NSTableView with multi-row cells (like, for example, Excel) for displaying more than a few words?

----

You can use a custom NSCell subclass, and do whatever you like.

----

can you have rows of different heights in one matrix?
I read in the NSMatrix class that this was not an option.  I think I've seen it, but I have yet to find any examples.

----

Check out RowResizableViews at [http://www.eng.uwaterloo.ca/~ejones/software/osx-tableview.html]

----

I can't get ReResizableViews demo app to run on Panther.  Anyone having any luck?

2004-04-17 08:57:42.612 RowResizableViews[10764] *** Assertion failure in -[iPhotoLibraryDataSource init], iPhotoLibraryDataSource.m:172
2004-04-17 08:57:42.614 RowResizableViews[10764] An uncaught exception was raised
2004-04-17 08:57:42.614 RowResizableViews[10764] Error getting albums
2004-04-17 08:57:42.615 RowResizableViews[10764] *** Uncaught exception: <NSInternalInconsistencyException> Error getting albums

RowResizableViews has exited due to signal 5 (SIGTRAP).

----

It worketh here using iPhoto 2. Do you  have the new version of iPhoto? Perhaps the library format changed. Just plug the code into your own test project.

----

That was it -- The library has to be called "iPhoto Library" and not just "iPhoto" or some other name.

----

One way I get around the iPhoto Library name thing is to get the library path from the com.apple.iphoto.plist file in ~/Library/Preferences. The library name changed from version to version ... the prefs file name did not. Just FYI ... ;-)

