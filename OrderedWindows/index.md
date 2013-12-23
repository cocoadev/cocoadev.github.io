---
layout: page
title: OrderedWindows
---

I need to traverse my windows in the front-to-back order they use on the display.

There is orderedWindows in NSApplication, but this one doesn't include miniaturized windows -- I could add these myself, but since it's not documented to not return miniaturized windows, I'd prefer simply to write my own implementation.

But how do I get the ordering of windows?

----

Miniaturized windows don't have any sort of order, since the only way to unminiaturize them is to click them and bring them to the front. I suggest that you get the orderedWindows, and then ask NSApplication for all windows, and add in any ones that weren't in orderedWindows. Depending on how you want your program to work, you could add them at the beginning of the array, or the end, or wherever it makes sense for you.

----

Yes, that's also what I do now -- as implied, I'd either like to avoid this or locate the documentation which says that the miniaturized windows are not part of the ordered windows (it does say that when a window is miniaturized, the window is swapped for an image, but clearly the window is still there).


----

Ordered windows are on screen. miniaturized windows are off screen and have no order...
If you want to get all your application's windows, I think you must mix  **windows ** to get all windows, and   **orderedWindows ** to get window order for visible windows.

----

Why do you need it to be documented? If you implement it correctly, then when Apple changes orderedWindows at some point in the future so that it includes miniaturized windows, your code won't break. Don't add miniaturized windows to the array unless they're not already there, and you're set. The only problem is that you take a completely inconsequential performance hit.

----

And when they change it at some point in the future to not include Expozay-ed windows, windows placed on another minitor or similiar, my code may break... is it really so far out that I prefer using documented behavior?

And no, you do not have to answer how I can also avoid that problem -- I was asking for a way to get the window ordering, since if this was obtainable, the code to do it right, w/o making assumptions and implementing workarounds if these should fail, would be much shorter!

----

My suggestion will not fail in any of the scenarios you present. The only assumptions my suggestion makes are that the windows whose ordering can be determined will be in -orderedWindows, and that -windows will return every window. Given the name of the method, I don't think those assumptions are very far-out.

