---
layout: page
title: ChickenOfTheVNC
---

If you've used VNC under OSX you've probably found **Chicken of the VNC** - you'd probably remember it! ;)

VNC has just received a major version update (June 2004), and the corresponding Chicken update has not yet been released. It's in CVS on sourceforge, though, and is an interesting read for networking things, Cocoa things (dynamic building of GUI elements, Keychain access) and more...

It's at http://cotvnc.sourceforge.net/

To get the version which builds, use
    
cvs -d :pserver:anonymous@cvs.sourceforge.net:/cvsroot/cotvnc login
cvs -z3 -d :pserver:anonymous@cvs.sourceforget.net:/cvsroot/cotvnc co -r GEN_2_GUI cotvnc


Then open it up and build in XCode... I'm using OSX 10.3.4 and XCode 1.2, and it builds perfectly :)

RufusCable

----

And for those who havn't, how about giving a summary of what **Chicken of the VNC** is?

----

VNC is a cross-platform remote access client and server (similar to pcAnywhere, Netops, Timbuktu, Carbon Copy, etc). **Chicken of the VNC** is a strangely-named Mac client (if you're looking for a server on the Mac, check VersionTracker for the current OSXvnc). For other platforms, have a look at http://www.realvnc.com/ - I use the Chicken to access Windows 2000 boxes a lot, and realvnc on Windows 2000 to access my Mac running OSXvnc. The connections can be tunnelled through SSH for security and get-through-firewall tricks...

I posted it here as I thought the code may be of interest :)

RufusCable

