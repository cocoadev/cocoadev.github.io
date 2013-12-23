---
layout: page
title: NSToolbarHelp
---

This page is a help page for problems with NSToolbar.
It hopefully be managed from time to time.
Unlike the other NSToolbar pages, this will be for problems with the source only.

For information about selecting a toolbar item or sending a toolbar action programmatically, see SelectingNSToolbarItem

----
Can you have a toolbar that uses many different controllers, that have different actions?
Such as 

Controller#1-------->(action#1)>                                Action#1

                                                        ToolbarController--<

Controller#2-------->(action#2)>                                Action#1

----

You can set the target in your toolbar setup code

    [ item setTarget: whateverController ];

----

How do I go about adding a NSSearchField and/or pulldown menus [like the action menu in xcode toolbar] into a toolbar for my app?

----

A toolbar item can contain an arbitrary NSView. Set up an NSSearchField or popup menu in Interface Builder and then shove them into your item when you set it up.

----

See NSToolbarItemSampleCode

See also GenericToolbar

See also NSToolbarTutorial

