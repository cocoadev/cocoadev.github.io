---
layout: page
title: ProgrammingWebViewObjects
---



I'm experimenting a bit with building a web browser; it might become a real software one day, it might just be tucked away as another experimental project.

Anyway, I'm trying to figure out the best way to implement tabbed browsing. When looking at the Safari NIB-file, one will find that they have a Custom NSView object for that purpose. Figures. So I've created an incomplete and ugly, yet working, subclass for implementing tabs. However, here's the glitch.

I can't add any WebViews! Kind of kills the purpose of it all, don't you think? Any other object works; buttons, text items, et cetera. But for some reason, the WebView just won't show up.

The API reference isn't exactly detailed. It simply says "... create a WebView object, attach it to a window, and send a loadRequest: message to its main frame". What does "attach it to a window" mean? I'm pretty new to Cocoa (though not to programming), so have I missed out on something here? Debugging my app reveals that, suprise surprise, my WebView doesn't have a hostWindow. How do I attach it?

Anyone feel like helping me out a bit?

*Is it an outlet I have to connect?*

