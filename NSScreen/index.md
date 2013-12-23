---
layout: page
title: NSScreen
---

A useful class for game makers or anyone needing to know the screen size.

Someone has not changed the code in this for a long time (check out this: + (NSScreen *)deepestScreen). 

Remember to always assume that someone has two or more screens so call this:
+ (NSArray *)screens

If you need more info than NSScreen gives you, use the deviceDescription dictionary to get at NSScreenNumber, which you can pass to CoreGraphics' CGDirectDisplayXXX family of functions.

----

Anybody know if there's an easy way to set what screen a window is on currently? It'd be nice if there was a way to just do a -setScreen: or something instead of fiddling with the frame. -- KentSutherland

There isn't. It doesn't really make sense to set what screen a window is on without fiddling with the window frame, does it?

And remember, windows can easily span two screens - or even more than two screens (I often test with three screen hooked up).

----

To get the screen rect, eg;

    screenRect = [[NSScreen mainScreen] frame];

----

To get the screen rect minus the dock + menu bar area

    usableScreenRect = [[NSScreen mainScreen] visibleFrame]

Actually this isn't entirely right. According to the docs,     -mainScreen returns the screen containing the current main window, which on a multiple monitor system is not always the screen with the menubar and dock. Use:

    usableScreenRect = [[[NSScreen screens] objectAtIndex:0] visibleFrame];


----

*Remember to always assume that someone has two or more screens*

Careful! Don't assume anything. In fact, be very careful not to assume there are any screens� you may be running on an Xserve.

----

Anyone had any luck with persuading SDL to open on a second screen?

----
Is there any way to get the would-be visibleRect, if the dock is auto-hidden? I know that if it's hidden, you get the screen height - 4 or something, because that's the tracing rect the Dock uses to know when to show (if the dock is on the bottom of the screen). And when the Dock is shown, it's the screen height - dockHeight. And I'd love to know if there's a way to get exactly that without the dock being always visible?

----
I am tring to find out if theres a way of detecting changes to the NSScreen screens so for example in my app I am creating fullscreen windows on each screen and I want to know when the screens change (removed added, resized) so I can adjust my windows. --TomLynch email me! me {AT} unknowndomain {DOT} co {DOT} UK

----
Take a look at NSApplicationDidChangeScreenParametersNotification. On a separate note, it's considered somewhat impolite to ask people to e-mail you; instead you should check back for answers.

