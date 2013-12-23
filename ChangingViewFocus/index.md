---
layout: page
title: ChangingViewFocus
---

Hi.  I have a window that contains an NSBrowser and three NSTableView view objects.  I expected to be able to tab to change keyboard focus to each view, but this is not the case.  How would I be able to cycle through each focus view?

----
Check out the "next key view" Outlet in IB .

--marcocoa

----
Thanks!  Now I have a followup question. 

I'd like to tie one menu item to each view in the window. Is there a way to determine which view has the focus?  I tried [NSView focusView], but that doesn't seem to give me what I want.  It always returns nil.

----
    focusView tells you about drawing contexts, and has nothing to do with UI focus. Look at the various information about the FirstResponder.

