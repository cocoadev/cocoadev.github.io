---
layout: page
title: HowToRenameTheExecutableInXcode
---



Assume you have created an Xcode project named Foo, which builds a Cocoa app named Foo.
If you wish later to change the Product name to Bar (i.e., the name of the app that is built),
there are several places you will have go to modify your bundle properties correctly:


*Change the name of the executable in the Properties pane of the Target inspector (this changes the CFBundleExecutable name)
*Change the Product Name in the Build panel of the Target inspector
*Confirm that the the name of the CFBundleExecutable key in the Info.plist file reflects this
*Change the CFBundleName in the InfoPlist.strings file so that the app menu will reflect the new name


If you do not complete these changes correctly, the most likely result on building and running in the Xcode IDE is that the running
app will not become frontmost, and although you can then select one of its windows, you will see no menu for the app in the menu bar.
If you try to run such an app in the Finder, you will be warned that the app is damaged or incomplete and cannot be opened.

These instructions do not strive for completeness or depth, but should suffice for those just learning the IDE.

