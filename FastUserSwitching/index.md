---
layout: page
title: FastUserSwitching
---

The capability of the operating system to effectively have two "Desktops" running, so you can switch quickly between them. A user's applications stay running in the background while another user's desktop becomes active.

----

I am looking for a method to programmatically bring up the login window but not log the current user out, similar to the FastUserSwitching menu item "Login Window..."

----

You're looking for a programmatic way to control FastUserSwitching. I believe the hooks for this are in Admin.framework. Unfortunately, it's in PrivateFrameworks, so all the normal caveats and pleadings ("you break it, you 0wn it") apply.

----

I know there's a command line tool inside one of Apple's frameworks that controls this. A popular FUS menu replacement uses this to work, although I can't remember the software name now (it's OSS if you find it). A search on www.versiontracker.com or in www.macosxhints.com (as I remember seeing the tool mentioned there once) could provide what you need.

*Maybe you're thinking of WinSwitch - [http://wincent.com/a/products/winswitch/]?*

----

A command line solution is outlined here: http://www.macosxhints.com/article.php?story=20031102031045417

    
#!/bin/sh
if  -z $1 ; then
  # robg note: Please enter the next two lines as one without
  # any spaces between the "/" and the "R"
  /System/Library/CoreServices/Menu\ Extras/User.menu/Contents/Resources/CGSession -suspend
else
  USERID=`id -u $1`;
  if  -z $USERID ; then
    exit -1;
  fi;
  # robg note: Please enter the next two lines as one without
  # any spaces between the "/" and the "R"
  /System/Library/CoreServices/Menu\ Extras/User.menu/ContentsResources/CGSession -switchToUserID $USERID
fi;


----

After a little investigation of the executable the command line solution above reveals (otool rocks). It looks like it may be a private method in ApplicationServices named CGSSwitchConsoleToSession..

