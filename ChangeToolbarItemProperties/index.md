---
layout: page
title: ChangeToolbarItemProperties
---

Is there way to change the properties (image, label, tooltip etc) of an NSToolbarItem during runtime, and have those changes reflected on the toolbar instantly?

From what I can tell, the toolbar is created with the delegate's     toolbar:itemForItemIdentifier:willBeInsertedIntoToolbar: on launch, but after that, I can't seem to find a way to alter the properties and have them appear in the toolbar.

Sending     setLabel: to the appropriate NSToolbarItem does cause a change in the object, however, the change does not appear in the toolbar until the item is removed and re-added via the customisation palette. Is there a way to force the toolbar to refresh/reload?

----

You may be able to do this via     validateVisibleItems on the NSToolbar

----

How are you changing them? Can you post your code? I change the properties of a toolbar item (specifically, its image) to indicate an on/off state. I have an ivar called tbbRulerState (to show/hide rulers). Using     toolbar:itemForItemIdentifier:willBeInsertedIntoToolbar:, in the block where I set up that toolbar item, I assign it to tbbRulerState. From elsewhere in my controller (the toolbar button's action selector), I say [tbbRulerState setImage:...]; and it works just fine.

