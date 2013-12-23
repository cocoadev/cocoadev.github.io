---
layout: page
title: HowToCheckThatMenuIsOpened
---



I would like to know, that a user opened some menu (Dock's menu for example). How can I check that?

I'll be thankfull for any help.

d.

----

Why do you need to know?

----

I want to display some information inside the menu (something like the info about currently played song in iTunes), and I don't want to update this information too often -- only when the user open the menu.

(Sorry for my english ;-)

d.

----

The easy way to do this is to implement     validateMenuItem: in the target of the menu item that you want to update. It's invoked just before the menu is displayed. It's intended to let you enable/disable the menu, but it works smashingly for changing states, titles, etc. as well. I'm a bit doubtful about this working for the Dock menu, though, but it's worth a try.

----

If it is just the dock menu, you can supply the dock menu dynamically. Have a look at the NSApplication delegate method "applicationDockMenu:".

BTW, your english is fine.

----

Many thanks for your help, guys! I'll let you now, if it works.

----

OK, both ways are fine for me and I don't know yet which method I'll use.

Thank for help :-)

d.

----

Have a try with "menuNeedsUpdate:" delegate method from NSMenu class.

