---
layout: page
title: MakeWindowVisibleAtRuntime
---

This is a newbie question (not difficult to figure that one out, huh?) How do we make a Window visible programmatically? I have two windows created using IB, and when clicking the button on one window, I would like to make the other window visible. I have been browsing through the methods in NSWindow, but failed to find one that suits. 

Will appreciate your help. Cheers!

----

The "quick" way: NSWindow's     makeKeyAndOrderFront:

The "right" way (assuming you're using window controllers): NSWindowController's     showWindow:

I would say RTM except you said you did. I suppose that, like me, the "make key" and "front" threw you off a bit. --JediKnil

