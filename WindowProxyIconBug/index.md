---
layout: page
title: WindowProxyIconBug
---

I can't get into the bug reporter to report this, states an exception happened...

Here is how to reproduce;

1. Place a window at the top of the screen touching the menu bar.

2. Put your mouse over the proxy icon (such as in xcode or text edit

3. Quickly click down and drag up, now you can freely move the window under the menu bar.


Make sure you click and drag fast, if you don't you will activate file drag. Also the title bar must be touching the menubar (ie. cannot move up more)

Enjoy!

----

I was able to easily reproduce this bug in General/TextEdit (Cocoa) but unable to in General/GraphicConverter and General/BBEdit (both Carbon) so it must be Cocoa only bug. And the window can be a few pixels below the menu bar, it doesn't have to be flush up against it.

That is FUBAR.  Looks like some inefficient code, too, b/c the minute you let the mouse button go it repositions... so checking happening where it shouldn't be, and not happening where it should be :)
