---
layout: page
title: ProgrammaticallyOpenAnNSMenu
---



Hi, I have an NSMenu object, I want to cause it to open programmatically. It's set to be the menu for an NSView. I've searched, but can't find anything like [menu display] or similar.

There must be a way to do this?

Thanks!

----
If you open NSMenu.h, one of the first methods you find is this:

    
+ (void)popUpContextMenu:(NSMenu*)menu withEvent:(NSEvent*)event forView:(NSView*)view;


----

Stupid-day, sorry.
--Sheepish--

