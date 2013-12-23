---
layout: page
title: SelectingNSToolbarItem
---

How can I programatically select an NSToolbar item?

----

    [toolbar setSelectedItemIdentifier:[theToolbarItem itemIdentifier]]

----

That only selects it, but I want to send its action too, so that it changes panes?

----

Just call the toolbar item's method manually. So let's say you have a toolbar item whose method is @selector(goHome:). You would simply use     [self goHome:nil] to call the method. Or     theToolbarItem target] performSelector:[theToolbarItem action] withObject:nil]

----

Yeah, I am aware I can do that :) But wondering if there was a way to do it otherwise. This is the way I have it done now, but it would seem better the other way (less code too). But thanks for your help!

----

Note that when manually calling actions, it's better to use code like this:
    
[[[NSApp sendAction:[item action] to:[item target] from:item];

This will correctly handle actions that go to the First Responder, and otherwise act the same for actions that have a specific target.

----
Is there any way to deselect a toolbarItem?  I am trying [toolbar setSelectedItemIdentifier:nil] and :@"" but can't make this happen.  Also, the toolbar variable _selectedItemIdentifier is @private, so I can't reference it directly in my code.
----
Your best bet might just be having a "Show All" equivalent like in the Panther system prefs, and using that. Alternatively, you could try having a "dummy" item in     toolbarSelectableItemIdentifiers: that isn't actually in     toolbarAllowedItemIdentifiers:, and switch to that one (since it looks like     setSelectedItemIdentifier: needs a selectable identifier). WARNING: untested! --JediKnil
----
Scratch that, :nil works just fine (a good night's sleep later the problem turns out to be on my end)

