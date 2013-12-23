---
layout: page
title: HelpNewUserInterfaceBuilder
---


I am a new developer and working my way through Cocoa Programming with XCode (2.4.1).


When I am creating a new class in InterfaceBuilder and choose "create files for ... " in the Classes menu the are created under "Other Sources" in my Project Window.

Is there a way to create my files under Classes instead of under "other resources"?? I searched the documentation, the preferences and the internet but couldn't find the answer.

Can somebody help me out?

Thanx Tom  

----
Just drag the files to the Classes group. All those folders are in Xcode are a fake directory structure that only Xcode knows about, it's stored in the project file. You can move things around from group to group and organize how you wish. --LoganCollins
----

