---
layout: page
title: IsMyWindowVisible
---

Does anybody know of a **fast** way of telling whether a window is visible.  Not if it's open, but if you can see any single part of it onscreen.  So long as no other window (or group of windows) obscures myWindow completely, I want the method to return yes, otherwise no.

----

This is just for your window's content view (typed in Safari, untested): 

    

- (BOOL) isWindowObscured
{
    return ( NSEqualRects(window contentView] visibleRect], [[NSZeroRect) || ![window screen];
}



I think there are private methods to get the window's entire view, including the titlebar, toolbar and whatnot.

*well, it didn't work... I'll leave it here in case anyone has any better ideas. You can use [window viewsNeedDisplay] to test whether part (not all) of your window is covered.*

How about just calling isWindowObscured? Not overriding it?

I'm prolly being really dense, but... um... I don't see     % - [NSWindow isWindowObscured]  or      %- [NSViewis isWindowObscured] so I'm assuming this is a category or ordinary subclass, but not override

