---
layout: page
title: DimmingCustomViewWhenWindowLosesFocus
---



I have a custom view which has two visual states, one for when the window is active, and one for when it is not active.

Question is, how do I determine that the window is not active?

Previously I used     NSWindowDidResignKeyNotification/    NSWindowDidBecomeKeyNotification, but these are also sent (i.e. window loses key) when e.g. one of the status bar menus open.

When you open a status bar menu, you do see the active window remove focus from its first responder, but it doesn't render the window inactive as if another window was brought to front (i.e. change the title bar, change the dark blue to grey in most views).

So what should I check in my custom view to get this behavior, where it is still rendered as active, when e.g. status bar menu are open?

----

As usual, I can't really wait for someone to reply ;)

Seems that a)     [NSApp keyWindow] is pointing to my window when the status bar menu is up (or other front-most blocking dialogs like e.g. Quicksilver), but b) this is     nil when re-activating the window. So the check should be:
    
BOOL isWindowKey = self window] isKeyWindow] || [self window] == [[[NSApp keyWindow];

Furthermore, you can get a resign key notification that doesn't trigger the dimmed rendering (as when opening a status bar menu) but then, if the user clicks outside your application to close the menu, the application will go inactive, but no new notification will be sent, so in addition I now also watch     NSWindowDidResignMainNotification and     NSWindowDidBecomeMainNotification.

