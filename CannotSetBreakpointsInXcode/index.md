---
layout: page
title: CannotSetBreakpointsInXcode
---



First a disclaimer: I'm new to Cocoa and Xcode, but I've been playing with it in the past, and never had this problem before.

Problem: even on newly created "hello world" Cocoa Application projects, I can't set normal breakpoints in the code. I've tried clicking in the breakpoint gutter in the margin of the editor: nothing; I've tried to ctrl-click in the breakpoint gutter in the margin of the editor: contextual menu items are grayed out; I've tried to put the cursor in the editor window, then select the "add breakpoint at current line" in the Debug menu, but it is grayed out.

My situation is the same as described in the only post I've found so far with a problem similar to mine: http://www.cocoabuilder.com/archive/message/xcode/2005/11/26/1501; unfortunately there is no answer; the main difference is that I'm using Xcode 2.4.1, while he was using 2.2.

I've restarted, recreated new bare bones projects, build, debugged, cleared all targets, and nothing. I remember last time I used Xcode (months ago, before upgrading to 2.4), I had no problems in setting breakpoints.

Any help or hint would be very welcome. Thank you in advance.
Gheorghe

----

I often have trouble setting breakpoints in Xcode after adding a new file to the project.  Quitting Xcode & reopening the project seems to take care of the problem.  Other things to check would be that the active executable is set correctly (under the Project menu).  After quitting Xcode & reopening the project you might also try a "Clean All Targets" followed by rebuilding.

----

I've tried them all  before; I followed your suggestions again. I also went again through some mailing lists, and apparently there has been other people experiencing this. Even a re-install of the developer tools does not help (http://www.cocoabuilder.com/archive/message/xcode/2006/2/19/4133).

Also tried all the sugestiions here http://www.cocoabuilder.com/archive/message/xcode/2006/2/16/4013, but, as I knew, everything was OK in the project settings.

Next step would be to re-installl the whole bloody OS X, but to me seems too much sincerely. May be there are some configuration files I'm missing? To be stopped by such a dumb problem when approaching a new development environment is really silly.

Gheorghe

----

I just re-installed the developer tools, and still it does not set breakpoints! I see that, even on in a freshly reinstalled Xcode, I still have a list of "recent opened projects", where I can find my previous projects; so, I guess that the uninstall script does not delete everything. Maybe there are also some settings kept somewhere. Do you know where to look for them?

Gheorghe


----
The list of recently opened projects is most likely stored in the preferences system.

defaults read com.apple.Xcode

You might want to move the preferences file altogether, in case something went haywire in there.
Move  ~/Library/Preferences/com.apple.Xcode.plist somewhere else and start XCode, see if it helps.

--CristianDraghici

----

How could I forget of the Preferences directory? Anyway, moved the file, and now it works! Thank you for the hint. I'm willing to go through the old preference file to see what was wrong with it, and update this post.

Gheorghe

----
----
This is an old thread but I just found this board recently, so I'll chime in.

I'm guessing that this is much simpler than you think. Either you're not selecting "build and debug" (you're running instead) or project menu's "set active build configuration" is set to "release." For debugging to work, your active build configuration must be set to DEBUG.

--Duncan C

