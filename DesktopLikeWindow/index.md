---
layout: page
title: DesktopLikeWindow
---

How can I create a window that is just above the desktop but below desktop icons, and doesn't go away with Expos�?

*Write a new operating system... Seriously this seems like a gargantuan hacking task. I know that Finder is responsible for displaying desktop icons (right...?) and Finder is a C++ application, hence it probably lacks runtime hooks like Cocoa applications have. Is there an ActiveDesktop-like OS-X feature people? I never used it in windows 97 but it might be cool on my fancy Mac (which can render graphics 40009009909 bajillion-zillion times faster than my old p133)* - JeremyJurksztowicz

----

Pessimism will not be tolerated :)

[myWindow setLevel:kCGDesktopWindowLevel];

-GusMueller

----

[jeremy ownedBy:gus];

----

Tried. Does not work. Obscures desktop completely.

*I think an interesting approach might be to use the kCGDesktopWindowLevel, but to copy the graphics for all the icons on the desktop and simply draw them on the DesktopLikeWindow. But this would be the diss Gus t'ing. Sorry. I just couldn't stop myself. Making it not go away when Expos� activates is left as an exercise for the reader.*

I think we can dispense with this. Your opinion may differ.

----

This will put a window above the desktop but below the desktop icons:

    
[myWindow setLevel:CGWindowLevelForKey(kCGDesktopIconWindowLevelKey) - 1];


However, the window seems to intercept all mouse events for some reason, but setting its alpha value to 0.0 will solve that if you don't need any interaction with the window.

-MarkusMagnuson

----

About a year ago, I was messing around with an "ActiveDesktop" application using code similar to Markus. Instead of changing the alpha value, I subclassed NSView and used the ignoreMouseEvents() function to stop interference with Desktop interaction. Seems to work with Expose as well. For the application's controls, I used additional NSViews placed stategically on the screen.

-BradGignac

----
Can you expound on this? As far as I know, this is only possible on a per-window basis with     -[NSWindow setIgnoresMouseEvents:]. If there was a way to do it per view (or even better, per pixel) that would be really useful. Thanks.

