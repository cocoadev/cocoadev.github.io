---
layout: page
title: TrackingNSMenuItemWhenUserNavigatesOverMenu
---

Is there a way to get the current selected General/NSMenuItem when the user is navigating over the main menu?
I want to display a small window beside the current highlighted menu with an example of what that specific option will do.

----
Is there something wrong with     - (void)menu:(General/NSMenu *)menu willHighlightItem:(General/NSMenuItem *)item;?
----

The fact that it is only available in 10.5 and later.
My application also runs in 10.4 

----

You can add tooltips to menu items.

----
Tooltips are for text only.
The solution for me is quite simple... - (void)setImage:(General/NSImage *)menuImage
