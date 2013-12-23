---
layout: page
title: HowDoMultipleScreensBehave
---

I have a question about multiple screen setups.

Is the (0,0) coordinate ALWAYS in the far left screen, even if the menu bar is not there? Is the Bottom Right Point of all Screens (allScreenWidthsAdded,0) ?
Is the Dock, when positioned on the left, always on the far left screen, even if there's not the menu bar? Same for when it's on the right side?

Thanks!

----
The (0,0) point is always at the lower left corner of the screen with the menu bar, and all other screens get coordinates relative to that. (For CoreGraphics work, the (0,0) point is at the upper left corner of this same screen, with the Y axis extending down.)

----
Is the Dock, when positioned on the bottom, always on the monitor with the menu bar?

----
No. If the two screens are above each other, the menubar is on the top monitor and the dock is on the bottom.

----
K, thanks. Another screen question.
The screen at Index: 0 (the screen with the menu bar) has a frame of (0,0,1024,768). The frame left to it, what frame does it have? (-1024,0,1024,768) for example? Does the origin change according to its screen size or is the origin always 0,0?

----
The global coordinate space is one continuous coordinate space. Your example frame is one possibility, but there is nothing which says that the Y coordinate at the origin has to be 0, since you can position other monitors so that they don't line up perfectly. The origin of the main screen is always (0,0) and the origin of the others always reflects their position relative to the main screen.
----
Alright, thanks. That wraps it up. When you say "main screen", do you mean [NSScreen mainScreen] (the screen with the active window) or [[NSScreen screens] objectAtIndex:0] (the screen with the menu bar)?

----
The latter. The coordinate system obviously does not change around just because the user clicked on a different window.

----
Of course, but I just wanted to be clear on that, for everybody who reads this. Thank you all for your help!

