---
layout: page
title: ClosingPrefsWindowWithCommandPeriod
---



I recently implemented a preferences window for a single-window non-document-centric app. I open the preferences window by connecting the prefs menu item directly to the orderFront: message. hitting command-w closes the window, so that's fine.

However, I've seen that in most cocoa apps you can close the prefs window with command-period, but I don't know how to make this happen in my app without making some sort of hidden mechanism that listens for that combo and closes the window manually. Is there something built-in? Like some mysterious way to say to cocoa "This window is the app's preferences window, set up the magic connections so I don't have to"

-- ShamylZakariya

----

If you add a cancel button (mapped to Esc as a key equivalent) hitting command-period will also trigger the button's action, which you can  have close the window.

----

If you make your prefs window an NSPanel rather than an NSWindow, then it will by default close in response to either escape or command-period.  -- Bo

----

Be aware though that if you bring up a sheet on a panel, it will activate the main window, when closing the sheet (and NSPanel doesn't accept being main window).

