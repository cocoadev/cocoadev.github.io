---
layout: page
title: WebViewImageDragInhibition
---

If I have a General/WebView in my app, but I don't want people dragging stuff out of it (i.e.) images, is there anyway I can tell the drag and drop subsystem to forbid dragging from the view? 

----

This is a futile thing to do because everyone can still take a snapshot of their screen. 

----

I'm not worried about someone copying my picts out -- you could open up the app package and pull the original files out.  Nonetheless, I want to know what the opposite of registerForDragTypes is, and how to prevent something from getting dragged.  In the app I'm working on, some of the stuff should be draggable (dragging it would have actual meaning) whereas some of the other stuff would only confuse the user if it were draggable.

**I've not enough experience with drag and drop directly, but if nothing else works, you could try to subclass, or use posing, or swizzling, or otherwise overload the drag and drop methods in the webview and implement them as you like.**

That's a thought... I generally don't like to subclass to *remove* functionality instead of *adding* functionality, but that might be the only way to accomplish what I'm after.  D&D doesn't seem to allow for deregestering drag types.

**I think they call that specialization.**
