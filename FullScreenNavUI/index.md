---
layout: page
title: FullScreenNavUI
---

Hi.  I'm new to cocoa and mac development in general.  I was wondering if someone could just point me in the right direction.
I'd like to build an application around a full screen UI similar to front row.  Where you have a list of items that may or may not take you to a deeper list in a tree hierarchy. Is cocoa the right framework for this or would I have to use carbon? Not really sure where to get started.

Much thanks!

----

Core Animation and NSView's new (in Leopard) -enterFullScreenMode:withOptions: may be what you're looking for. -- EmanueleVulcano aka millenomi

----

"Full screen mode" can be separated into two problems: 1) Taking over the whole screen, and 2) Animating entering fullscreen mode.  If you're targeting Leopard, -enterFullScreenMode:withOptions: covers both bases.  If you're targeting Tiger, then you can use NSScreen to get the size of the screen, set the frame of whatever window you want to be fullscreen to the size of the screen (make sure the window has had the borderless mask applied), and then use SetSystemUIMode (a C function) to hide the dock/menubar if you like.  The actual animation can then be done with NSAnimation.

----
See also: BreakItDown

Cocoa is just fine for this. However it's a large problem with many subproblems, and you need to BreakItDown to identify and solve the subproblems individually.

enterFullScreenMode seems to stay /above/ the login prompt (when waking from sleep) - this seems like a very bad thing!

----

OK so what about mimicking the UI that Front Row has, where there's a scrolling display of files that always has one selected?  What sort of components make up that view?

