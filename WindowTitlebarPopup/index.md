---
layout: page
title: WindowTitlebarPopup
---

How do I create a window titlebar popup (with associated icon) when the popup isn't a represented filename?

General/OmniWeb and Safari do this.

----
You're going to have to be a bit more specific than this. What exactly are you talking about? I know of no popups in Safari's title bar.

----
Command-click on the name in the titlebar in either Safari or General/OmniWeb. This is similar to the "path popup" you get when the title is a represented filename, but there is no represented filename here.

I don't see public API to do this. How does General/OmniWeb put the proxy icon there? How do both intercept and do the path popup?

----

Ah! I never even knew that was there. :-)

I imagine it's as simple as adding a tracking rectangle to the window at the right coordinates / size. Of course you'll need to keep the rect updated as the window is resized, etc. When the mouse is clicked in that rect, ask the event for its modifier flags to see if the Cmd key is pressed. If so, pop up a menu at the appropriate coordinates. As for the images in the menu items, there's documentation all over the place for how to do that.

----

Building the menu is easy. It is intercepting the click (and or providing the icon next to the title as General/OmniWeb does.) A tracking rect doesn't allow me to intercept clicks. Some other hackery is going to be necessary.

----

My apologies, you are correct - tracking rectangles only provided entered/moved/exited events. Still, you want to intercept mouseDown on your window and check to see if the event occurred in the title area (or wherever).
