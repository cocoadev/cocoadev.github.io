---
layout: page
title: MakingAnNSDrawerRemainTheSameSize
---

If I have a parent window to which I've attached a drawer, and I resize my parent window, and the drawer is attached to the right side of the parent window and I want to the drawer to remain the same size regardless of the parent window's size (or resize), what can I do?


----

[drawerView setAutoresizingMask:NSViewMixX];

I think --zootbobbalu

----

Not sure what you mean by "NSViewMixX" but I tried all reasonable variations... didn't work... :(


----

Oops, I'm on drugs!! That's NSViewMinXMargin

----

Any other thoughts?  I'm still struggling...

