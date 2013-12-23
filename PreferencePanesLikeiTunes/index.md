---
layout: page
title: PreferencePanesLikeiTunes
---

How does one make the preference panes like iTunes? I guess they are bundles..
How is the framework used and called?

----

This is nothing magical. Especially for Panther and up (doing the shaded selected toolbar item thing). It's probably good practice to have the preference panel in a separate nib, but not strictly necessary. You have the prefs window and one NSView for each panel. Depending on the selected toolbar item, view 1 is removed from the window's contentview, the window is resized (animate:YES) to view 2's frame size, then view2 is added to the view. Makes for a simple and pretty prefs panel.

Check out AutoWindowResizing and (most importantly) use the search function for more information here.

----

Look in ObjectLibrary for UKPrefsPanel. There's also a bit of UndocumentedGoodness called NSPreferences, but you're safer with code you actually control.

----

Just use an NSToolbar, and change the window's content view when a different tab is selected.

----

Many thanks for the help!!

----

Check out this post for a working version of what you're describing: http://johndevor.wordpress.com/2006/12/24/simple-preferences/

