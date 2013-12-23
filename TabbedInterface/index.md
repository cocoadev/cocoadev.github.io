---
layout: page
title: TabbedInterface
---



I'm looking to create a tabbed interface that is driven by a drop down menu (rather than tabs).  Exactly like the Interface Builder's 'Info Panel' works.  But I'm unsure exactly which elements to do - Currently I'm just creating 'boxes' (over the top of each other) and setting there 'initial state' to 'hidden'.  This is obviously not the way (as it's extremely cluttered to work on).

Any ideas/advice/suggestions?

Thanks

General/NSBox -setContentView:

*Um, no, don't worry with setContentView ... use the tabbed view. In IB, turn off the tabs for your view, create an outlet and connect it to your tab view. Create an action outlet and connect your popup button to it. When the action is fired, get the [sender tag] (remember to set them for the combo's menu items) and tell the tab view to switch to the correct tab item. For the specifics to the 'but how' questions, you'll definitely want to read up on General/NSPopupButton, General/NSTabView, and General/NSTabViewItem. You'll also need to understand Interface Builder, outlets, actions, etc.*
