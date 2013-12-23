---
layout: page
title: SystemWideKeyboardShortcut
---

I'm wondering how some of the OS X utilities out there (General/LaunchBar or General/QuickSilver for instance) are able to "catch" keyboard shortcuts and perform a subsequent action (like make a window appear). In other words, how you create a system wide shortcut? I'm what you might call a "rusty" intermediate Cocoa programmer...much better at OOP than procedural C type programming...so if you could just point me in the right direction API-wide, I could probably figure it out unless it's really low-level stuff.

Basically, all I want to do is to be able to be notified when the user hits Command-Space or something similar. I know what to do from there. :)

Thanks,

General/JaredWhite

----

Look at my working sample code at http://www.egeronline.com/add/download/hotkey.sit

----

The keyword you're looking for is General/HotKeys. See also General/GlobalHotKeys and General/CarbonCompatibility.

* Ah, thank you! I never thought of using that term, so that's why I couldn't find anything here. Awesome! -- General/JaredWhite *
