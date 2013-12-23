---
layout: page
title: HowToMakeAWindowImmovable
---

Hi all,

Is there a way to make window immovable, even upon dragging the window?

Thanks

----
Sure. Subclass NSWindow and override -setFrame: ... however, there are very VERY few instances where you should do this. The only one I can think of at the moment is a splash screen. Also, if you are going to do this, make sure you remove the title bar, etc. so the user doesn't think they *should* be able to move it.
----
Removing the title bar will make the window immovable, you shouldn't need to subclass anything.  Just don't pass     NSTitledWindowMask (or whatever it's called, something to that effect) to the NSWindow constructor and it should work fine.

