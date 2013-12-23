---
layout: page
title: ClosingAWindowGotMeConfused
---

As far as I can tell, there are lots of methods to close a window.
performClose:
orderOut:
close

So, the obvious question is: Which one to use? And which method sends the delegate method windowShouldClose and windowWillClose?

Thanks.
----
-orderOut: doesn't close the window.  It only takes it off the screen.
-performClose: is called when the user clicks on the close button.  If you call it, it is the same as if the user clicked it.
- close actually closes the window.  -performClose presumably calls close.

----

To use performClose is probably "kinder" and easier for you later, unless you wish to override notifications or quickly move past user actions:
- performClose highlights the button before closing, and calls NSWindowWillCloseNotification. It also checks with the delegate to get booleans as to whether or not it should actually close. If you think you'll need that later, it's good to go this route.
- close: is indeed called by performClose after everything else is done.

- close:, called by itself, doesn't check its delegate first to see if it's allowed to close or not. peformClose: does that. Something else to think about.

-- JasonTerhorst

