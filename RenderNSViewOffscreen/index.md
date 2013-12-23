---
layout: page
title: RenderNSViewOffscreen
---



Hi,

I have been getting NSImages from an NSView (WebView actually) via an NSBitmapImageRep intermediary and all is great. However, what if I don't want my NSView/WebView to actually appear on screen - all I'm interested in is just getting an image of its rendering? I tried to create the WebView programmatically rather than via IB, and grab an image from it the same way as before - but all I get is white.

The problem surely lies in the following piece of code, which works fine when the webview is rendering onscreen, but I don't know what to replace it with if the webview isn't in the GUI:

    
NSBitmapImageRep* rep = [[NSBitmapImageRep alloc] initWithFocusedViewRect:[webView frame]];


Any help would be most appreciated, thanks!

----

The docs for that method say it assumes the currently focused window. So if your view is not in a window, that isn't a true assumption. Question is: what's going to make your view actually draw? Something needs to trigger its drawing method to actually start rendering its content, and if it's not part of a normal window or drawing hierarchy, you'll have to arrange that to happen yourself. For example by calling its -display method. --GrahamCox

----

-Hmm, good point on the calling the draw method issue - but it's not a problem, I can just call '-display' manually. I suppose that leaves the quesiton of how to capture the drawing of a view that isn't on-screen (is it even possible?), I can't seem to find a suitable replacement for NSBitmapRep's 'initWithFocusedViewRect'. Any advice?

----
You might try dataWithPDFInsideRect:, I believe it will work in this situation. You can then load that data into an NSImage and do with it what you will.

Another alternative is to put your view in a window anyway, just don't actually put the window on screen. So long as you pass NO to     defer in the initializer, the window will still exist even if you never display it, and things that require windows should work with it.

----
One thing I noticed (w/ NSTextView) is that if the view is off-screen -display (and friends) may not actually do anything - the content may not get laid out - and in that case a call to -sizeToFit may(/does) fix it. dataWithPDFInsideRect: is the route I've taken (I'm currently doing similar) and I forgo using NSWindow for optimization purposes, like it makes a big difference.

