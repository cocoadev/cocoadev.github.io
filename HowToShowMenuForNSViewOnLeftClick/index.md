---
layout: page
title: HowToShowMenuForNSViewOnLeftClick
---



I just know this is gonna be a dumb question but:

I have a NSView subclass that I just want to show a menu when it receives a     mouseDown: event, but I can't find any way to just tell a NSMenu to show itself...

I'm using     [NSMenu popUpContextMenu:[self menu] withEvent:theEvent forView:self] in my mouseDown, but that shows a contextual menu, with items added by CMM plugins, which I *don't* want. I need to either get rid of those or just show a regular menu. Is there any way to do that?

This is for a NSStatusItem, but I need to use a view so I can support DragAndDrop.

----

To my knowledge, Cocoa actually doesn't allow this since NSMenuView has been deprecated. However, MenuManager has functions for popping up regular menus at arbitrary locations.

*I've gotten it working, more or less. The solution was to have my view contain a NSPopUpButtonCell, set the menu on that, and send it a     performClickWithFrame:inView: message in my view's mouseDown. The menu's positioning is still wierd, but I suppose fiddling with the frame I pass to the cell might do something... Where are the MenuManager functions documented? Is it Carbon? Can I pass it a NSMenu?*

MenuManager is the Carbon menu API. Its functions take a MenuRef, but there's some UndocumentedGoodness on the NSMenu page that lets you get a MenuRef from an NSMenu.

----

In 10.3+, there's NSStatusItem's     -popUpStatusItemMenu: for just this purpose.  -- Bo

