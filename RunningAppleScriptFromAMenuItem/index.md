---
layout: page
title: RunningAppleScriptFromAMenuItem
---



Is there an easy way to select a menu item and have it run an AppleScript solely using Interface Builder?  E.g.  Selecting the "Play/pause iTunes" menu item would run the applescript "tell application "iTunes" to playpause".  I've poked around, and thought that the "AppleScript" pane in the inspector might help, but I couldn't figure it out.  Thanks.

----
How about writing a one-line action method that uses NSAppleScript?

----
----
My goal is to get something running solely in IB (I'm trying to monkey with a .nib file, so I don't have the source.)

