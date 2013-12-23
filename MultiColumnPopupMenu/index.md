---
layout: page
title: MultiColumnPopupMenu
---

I know that this violates Apple Human Interface Guidelines, but I want to create a multi-column menu.

In particular this is for the Cocoa app Fire http://fire.sf.net.  I want the popup menu to display all of the smileys in one convenient rectangular grid and call the appropriate selector based upon the chosen smiley.  So far we are using NSMenu with submenus/etc, and it is painful to use.

I would like to pattern this after the popup used on some Windows clients (Like Trillian and Yahoo! Messenger)

Any ideas on how to best do this?  NSTableView, something else...

Alan

----

How about placing an NSMatrix in a borderless window and having it display at the appropriate location whenever the user clicks on a button? That way you don't have to struggle with NSMenu/NSMenuItem to make it do something it wasn't designed to do. -- RyanBates

----

Indeed, that's the way iChat does it (custom window and tracking). You can make your custom window look just like a menu by using the NSColor methods for getting system colors. -- JosephSpiros

