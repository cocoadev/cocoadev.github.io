---
layout: page
title: HowToDisplayPackageAsFile
---

The discussion is essentially closed. The answerToTheQuestion was found!! Feel free to add any comment at the end.

----

I use 'package files' for the documents of the application I am writing. I set this in the inspector window of the target, by clicking on the appropriate checkbox (in XCode). However, in the Finder, the documents still do not show as files, but as folders. This is fine for now, because I am the only user of the app, but I would like future users to not have to deal with this. Are these 'file packages' supposed to show as regular files (like icns or nib files)? How and when is the Finder aware of it? Does the app have to be in a particular path where it will be known by the system? I tried to put the product in /Applications, and quit-start the Finder, but it is still not showing.

Another point: I used the same file extension for a while for regular files, and now shifted the app to use packages instead, so maybe there is a potential conflict here?

Thanks for your experience/input!!

CharlesParnot

Have you tried restarting the system? Finder may need more encouragement to get rid of its cache. Also make sure there aren't any old builds of your app that used the non-package format lurking about.

----

A restart shouldn't be required.  As usual, a reboot should do it.  I do agree that this is probably the issue.

*? Restart, reboot; what's the difference?* 

*I guess he means logout?*

----

** answerToTheQuestion **

I found the answer, thanks to a comment done here. I actually had an old build "lurking around", not even in /Applications, and a newer version that I put in /Applications would not have priority. Even after rebooting (or restarting?? ;-), the files would still not display properly. This is when I realized about this old version. As I wanted to keep it, I made a disk image with it to make it disappear. Then, simply quitting and restarting the Finder would do it (I also put the application in /Applications, because I think I remember this place is being scanned for info.plist files, but I don't know if this is necessary). Yeah!!

----
**Comments**

