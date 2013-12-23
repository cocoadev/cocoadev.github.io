---
layout: page
title: AcceptingScrollWheelWithoutBeingKey
---

 

Can I somehow accept scrollwheel events when the mouse is over my window, even though the window is not in the foreground?

----
Drawers can do it even though they're never really in the foreground. I don't know how they do it but I'd try calling     [window becomeKeyWindow] ...

'window' won't become main but it should become key so that you can snag     scrollWheel: events. The docs say not to call     becomeKeyWindow so quirks and future breaking should be expected.

----

Yeah drawers do it somehow :-/ becomeKeyWindow doesn't seem to do anything (I put it in mouseEntered).

----

If you only want to catch these events when your application is frontmost, this should be possible to hack. Override NSApplication's -sendEvent: and have it send scroll events to your window when the cursor is over it (but not over a window in front of it), and I think everything will Just Work from there.

