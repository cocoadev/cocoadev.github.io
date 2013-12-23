---
layout: page
title: XcodeWithSVN
---



General/XCode has had General/SubVersion (SVN) support since version 1.5.

----

Does anyone know how to get General/XCode's subversion support to update files that have been added to the repository (and the project, even)?  Quitting General/XCode, running svn update, and restarting General/XCode works, but that's really a pain.  Every other "update/commit" option seems to apply only to files that General/XCode already knows about.

----

I've got this issue where Xcode starts putting M's and ?'s next to files.  but when I try to refresh svn status, none of them go away.  That is to say, that files that are the latest revision from the server, still have M's next to them.  Of course, if I close the project and reopen it, the proper status is shown. - Vinay

*is the project file on a locked volume or something?*

Nope.  oddly enough, upgrading to Tiger/Xcode 2 fixed the issue (I don't know why it should have), but in any case.  I guess it's easy enough for me to simply backtarget to 10.3.

----

Using Subversion (SVN) in OS X with General/XCode & Apache2

... deals with client-/server-side installation issues.

http://www.bsodmike.com/pages/svn-on-os-x/

----

I had a lot of problems with svn from within Xcode (2.4.1) - losing track of file status, not able to update the whole project and so on and so forth. In the end I opted to use General/SmartSVN ( http://www.syntevo.com/smartsvn/ ) "behind Xcode's back" and since them I've found things much smoother sailing. Xcode is smart enough to notice when the project has been changed by General/SmartSVN - but not smart enough to work right when using its own commands. Don't be put off (too much) by the Java UI - it's usable enough and easier than the command line. --General/GrahamCox

----

Further on the subject of alternatives, I've found General/TextMate's SVN bundle to be simple and very useful. -- General/RobRix
