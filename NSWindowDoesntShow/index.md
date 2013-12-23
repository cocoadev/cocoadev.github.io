---
layout: page
title: NSWindowDoesntShow
---

    
	NSWindow *myWindow = [[NSWindow alloc] initWithContentRect: NSMakeRect(10,10,50,50) styleMask: NSBorderlessWindowMask backing: NSBackingStoreRetained defer: NO];
	[myWindow setBackgroundColor: [NSColor clearColor]];
	[myWindow setHasShadow: NO];
	[myWindow setOpaque: NO];
	[myWindow retain];

	[myWindow makeKeyAndOrderFront: sender];
...
...
	[myWindow orderOut: sender];
	[myWindow release];


Why doesn't this show my window ? When I used IB and made a window and an outlet, much the same code worked. So I'm wondering what my error is. I just want to show a gif animation for x seconds and thought this would be a good idea...

----

It's not showing your window because you're making a completely transparent, shadowless window with no contents and no titlebar. It's probably "showing" your window, but since there's nothing in it that would be visible, you wouldn't be able to tell.

On a completely unrelated note, the retain is redundant (and will in fact cause a leak), because you already alloc'd the window.

One other thing, you really should use     NSBackingStoreBuffered instead of     NSBackingStoreRetained. The latter will disable the double-buffering which makes OS X's windowing system so nice to use.

