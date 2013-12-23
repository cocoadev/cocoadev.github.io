---
layout: page
title: MenuTitleFeedback
---

Similar to how e.g. the "Edit" menu title highlights when the user press cmd-c (Copy) I want to highlight the title of another menu, when the key equivalent of an item in this menu is pressed.

The problem is that the key equivalent is actually sort of a sequence, so it's too complex to be handled by the item itself. So I handle it in the NSResponder which is the target for the menu item.

But is there any way I can invoke the highlight behavior? 
----
See:     [NSMenuView -setHighlightedItemIndex:] or     [NSMenuView -performActionWithHighlightingForItemAtIndex:]
----
Ah, thanks! But one follow-up question: How do I get from an NSMenuItem instance to the associated NSMenuView?

----

Will the suggested method even work? See this note in the docs:

**Note:** NSMenuItemCell and NSMenuView are deprecated and are no longer used to draw menus. Calling their methods will not affect the appearance of your menus.

----
Some of it still works, such as: + (float)menuBarHeight

*But they still don't have anything to do with the drawing of menus in Mac OS X.*

