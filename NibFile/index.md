---
layout: page
title: NibFile
---



A NibFile is a file bundle created by InterfaceBuilder which contains information about an application's user interface and object interactions. One application may have many nib files, for each of its various windows, supported document types, et cetera.

The advantages of using a NibFile are that your program does not have to define the user interface programmatically, and also that you get to use the excellent user interface tool, InterfaceBuilder. It allows you to very quickly compose the user interface of your application; traditionally one of the most complex parts. It also makes it possible to change the interface on the fly, without having to recompile.

See also http://www.stepwise.com/Articles/Technical/FreezeDriedObjects.html for an excellent explanation of what a NibFile is, just be sure to mentally replace every instance of "OpenStep" with "Cocoa".

"Nib" comes from "NeXT Interface Builder".

----

If a NibFile is loaded and then the UI objects it created are modified, are those modifications ever written back to the NibFile?

----

No.  And there's a few good reasons for that IMHO:

*Since the nib is in the app bundle, changing it would change the setup for all users rather than just the current user, an incorrect behavior in most situations,
*Since the nib is in the app bundle, there's no guarantee that you'll be able to make changes to it at all.  For example, if the app is copied to a NetInfo server and accessed as /Network/Applications, you will not be allowed to write to it.
*It's usually easier to code one case, loading a nib as a template and programmatically making changes to it as needed, then to figure out what sections of the nib are up-to-date and which parts need further modification.

Some nib objects (most noticeably NSWindow and NSTableView) have an 'Autosave Name' field in Interface Builder that will allow them to save some very basic preferences (e.g. the default window size and location for NSWindow); however, these are saved in your app's preferences not to the nib file. -- Bo

----

Can an application other than InterfaceBuilder read and/or produce nibs? I've been unable to find a way in which nib files could be modified or even inspected after loading (with the exception of /usr/bin/nibtool, but I didn't want to spawn a new process and pipe the output if it was possible). -- l0ne

*It's possible, but the NibFile format is private, so it wouldn't be easy. Take a look at Renaissance - * http://www.gnustep.it/Renaissance/

Yes, I know, but I'd have loved to create a small .nib tool to complement regular Cocoa development. Any pointers, anyone? -- l0ne

----

Apple has some documentation here [http://developer.apple.com/documentation/Cocoa/Conceptual/LoadingResources/index.html] Maybe the NSNib class is enough.

There are a few ML threads archived on cocoabuilder.com; search for 'nib format'

