---
layout: page
title: WebKitPlugins
---

Has anyone noticed that using webkit to display flash content, whether it be by loading an HTML page with embedded flash content or loading a .swf file directly, that the flash animation slows down to about a frame a second when the cocoa app hosting the General/WebView is not key?  When the app has focus/is key the flash content zooms along at it's normal speed.

I'm working on a cocoa app that will host flash content via a General/WebView and have just noticed this.  I was wondering if anyone else could confirm this.

--Paul

----

It happens in Safari as well, with flash banner ads and whatnot. It may be better that it doesn't redraw them as often, because it could really slow down other apps if it did.
