---
layout: page
title: GlobalFloatingWindow
---

I'm trying to get a global floating window in my app, that works along the same lines as DragThing or Quicksilver does- a window comes up in the current application, but any mouse events to it does not cause my application to come front.

I've got the first part working fine (subclass NSPanel, [window setLevel:kCGUtilityWindowLevel], but anytime I hit a button in my window, my app comes to the front.  I don't want that.  I want it to stay in the background so the menu bar for the current application stays the same.

So the question is, what do I have to do to get the behavior I want?  TIA.

----

Make your app a faceless background app with LSUIElement.

----

Using LSUIElement is one way, but I don't want my app to be faceless.

----

Try making your panel a non-activating panel

*NSN<nowiki/>onActivatingPanelMask
	
The panel can receive keyboard input without activating the owning application. Valid only for an NSPanel or its subclasses; not valid for an NSWindow.*

----

That was it! Thanks.

I should point out that that docs say it's "NSNonActivatingPanelMask", but in NSPanel.h it's NSN**'onactivatingPanelMask- note the non capital 'a'.
 
So     NSNonactivatingPanelMask must be used in the code.

