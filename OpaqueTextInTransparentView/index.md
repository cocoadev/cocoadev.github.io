---
layout: page
title: OpaqueTextInTransparentView
---



I have a transparent NSTextView and the problem is that the text there drops a shadow to desktop, and so any editing results in 'dirty' text/shadow. Calling invalidateShadow each time is expensive, so I want text to not draw shadow at all, like terminal.app does. If you make a fully transparent backgroud in Terminal, you'll see that even it's window title doesn't have shadow (but the rest of the window does). I assume Apple did something tricky in Terminal textview, but I can't figure out what exactly. Searching archives reveals this question arise again and again over years, but no one did post a solution to that.

Any ideas?
--AK

----
I, too, would be interested in how to achieve this. So here's a "bump".

----
Yes, also, when scrolling through a text that's within a transparent view, it's really ugly to see the old text there until the scrolling bar is released. Is there no simple or quick way to turn off drawing of shadows in views?
Thanks.

----

Put the transparent view into its own window and make it a child of the other. This way you can turn the shadow off in the child and the parent can have a shadow.

----

If you go the child/parent route you'll need a transparent view in the parent window as well, which I call the placeholder view. The placeholder view, depending on how you want to do it, can either post frame changed notifications (for the textview [or its controller] to pick up and act on) or it can be set up to size the textview(/child window) more directly.

