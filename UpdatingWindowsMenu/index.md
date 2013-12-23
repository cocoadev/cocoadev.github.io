---
layout: page
title: UpdatingWindowsMenu
---

I'm creating an app, and I'm wondering how to make Cocoa automatically populate my "Window" menu.

My dock menu populates with the contents of General/NSApp:windowsMenu, but how do I put this into my "Window" menu, which I'm creating manually through API (as opposed to to Interface Builder).

Naming the menu "Window" doesn't appear to be enough (as opposed to "Edit", which Cocoa picks up and adds items for).

Anyone familiar with this?

----

You're so close... look up     -General/[NSApplication setWindowsMenu:].

----

I've created a menu obj (since it is nil by default) and used General/NSApp:setWindowsMenu to get it in place. This enabled window lists in the dock menu (since windowsMenu was nil before, it would show nothing except the default items like Quit).

I'm not sure what to do for the main menu, though. I already have a "Window" menu that I've populated with various utility functions unique to my app, and I'd like Cocoa to dynamically generate a window list and put it in there as well. Setting General/NSApp:windowsMenu to my toolbar menu may work, but what will happen to the Dock menu then?

----

There isn't enough information given to advise. This is a feature that normally "just works". Why are you creating the menu programatically and not using IB? No doubt you have your reasons but without knowing them, the obvious answer is to swim downstream and do it the easy way. Note - there is no difficulty with adding extra menu items and commands to the standard Window menu in IB, these items work normally, with the window list part of the menu managed automatically. I do this all the time. --GC

----

I've found the answer, so here's the solution for posterity's sake:

- General/NSApplication:setWindowsMenu should probably be named "addWindowsMenu" instead. The "set" implies that it may only track one menu object at once, where in reality every menu you want automated through Cocoa can be added by using "setWindowsMenu". Calling General/[NSApplication setWindowsMenu] on my menu worked perfectly!
