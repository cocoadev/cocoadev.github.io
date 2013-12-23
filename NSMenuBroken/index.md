---
layout: page
title: NSMenuBroken
---

In 10.3.x, NSMenu appears to be broken.

[[[NSApp mainMenu] itemWithTitle:@"Utilities"] setSubmenu:[mode utilityMenu]] causes a crash.
There is definately an itemWithTitle:@"Utilities" (I can modify the titles of the items it contains), and [mode utilityMenu] is definitely not nil. Can anybody tell me what is going on?

Update:
[[[[[NSApp mainMenu] itemWithTitle:@"Utilities"] submenu] itemAtIndex:0] setSubmenu:[mode utilityMenu]]; works fine, except that its a hack and shouldn't be necessary. Argh.

----

There's probably something sacred about the menu bar that prevents setting the menus referred to by its items.

I'm kind of confused, though, because it doesn't seem like your second code snippet should do the same thing as the first one � the first one would give you a different menu when you clicked on Utilities, whereas the second would give you a menu with utilityMenu as the submenu of its first item, right?

