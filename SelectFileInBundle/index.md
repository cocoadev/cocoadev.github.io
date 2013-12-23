---
layout: page
title: SelectFileInBundle
---

I am trying to use NSWorkspace's selectFile: method to select a file located inside a file bundle.  The method works as expected when used on a normal folder, but simply opens the folder containing the bundle if I attempt to select the file inside the bundle.  Any ideas?

----

Run an NSTask with "open <pathToFile>"

*But not before making a NSURL out of <pathToFile>, passing that to NSWorkspace's     openURL method and seeing if that has the desired effect.*

----

A-ahem. I believe the original poster was NOT trying to open a file but rather to reveal it in the Finder. The only alternative to selectFile: I know of is the Finder's AppleScript dictionary, which should work (but I didn't try).

*URLs can point to folders too.     openURL may not select the proper file, but it'll get the folder inside the package opened.* Whoops, you're right.

You can also send the finder an apple event.

