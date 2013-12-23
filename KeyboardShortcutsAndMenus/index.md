---
layout: page
title: KeyboardShortcutsAndMenus
---

I'm building an interface that needs to accept the left and right arrow keys.  I don't want to have button, so one way of making it known to the user that the app accepts arrow keys is to have corresponding menu items.  Only problem is, if I stick the arrow keys in the menu then when I'm editing a NSTextField or view the menu gobbles up the keyDown events.  Disabling the menu items doesn't help and using a modifier is not an option.  Accepting the arrow keys themselves is no biggie -- I can just go to work on the appropriate control's keyDown and up methods and have the responder chain automatically know how to handle arrow key events.  However, then the arrow keys aren't listed anywhere in the UI and help files are teh suck.  Any ideas on how to let the user know they can user the arrow keys with my control?

