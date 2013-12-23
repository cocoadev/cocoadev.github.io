---
layout: page
title: HowToKeepWindowRefreshingWhileInMenus
---

Since 10.3, when a menu is pulled down from the menubar, all animation in the application's window is paused until the menu goes away. I remember reading someplace that some piece of code needs to be added to main(), does anyone know what it is?

Thanks :)

----

In 10.3, menus are treated like other controls and a different runloop mode is used when tracking them. If you want a timer, NSConnection, etc. to continue working, you have to tell them to run in that mode as well. The mode in question is     NSEventTrackingRunLoopMode. Note that this will make your animations happen while the user is holding the mouse down on a button, slider, etc. as well. There is no way as far as I know to make animations happen while a menu is held down but not while the user is tracking a slider.

*Can anyone think of a reason why Apple would want to make OSX act like OS9 in this respect? Seems to me this is a HUGE step backwards.*

----

I beleve this is either for performance or some temporary bugfix.

----

It makes AppKit more consistent. Before, in 10.2 and earlier, controls like sliders and scrollbars used different runloop modes (effectively freezing 'normal' timer activity etc.), but menus didn't. The reason for using different runloop modes is simple. You want to be able to provide a smooth user experience, so heavy users of CPU time should be temporarily stopped. The change with menus in 10.3 resolves this inconsistency. Now all controls that involve mouse tracking behave the same. Since making your program continue to run in these runloop modes is trivial if you should desire to do so, this is not at all a step backwards.

