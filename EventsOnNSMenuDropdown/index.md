---
layout: page
title: EventsOnNSMenuDropdown
---

I have pored through Google and the Apple docs, but find no mention of any such functionality. I'd like to trigger a function upon menu dropdown. I have a menu whose items need to be added dynamically at run-time to match a list elsewhere in my program. I figure the best way to do this is to update the menu's items every time it's dropped down.

I have attempted to set a selector to the menu's parent item, but apparently is only called if the item is activated like a conventional menu item (i.e. the item has no submenu attached).

Anybody ever tried this before?

----
Open NSMenu.h, search for the word "delegate", and you'll be on your way.

