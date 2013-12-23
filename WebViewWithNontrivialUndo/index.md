---
layout: page
title: WebViewWithNontrivialUndo
---

I'm working on an application which uses a General/WebView that interacts with some external controls. The controls modify my Model class, which generates some HTML and outputs it into the General/WebView by loading a temporary file.

This was all well and good until I tried to add undo support to my application. I tried subclassing General/WebView and setting undoManager: to
return the same General/UndoManager as my Model, which worked until a page reload was required, at which point the General/UndoManager seemed to push
only the General/WebView's undo commands off the stack. This sort of makes sense, since I imagine that the intent of the designers was that if I'm reloading a page I'm probably opening up a new document and won't need those actions anymore.

I've found one example of seamless undo with an General/NSTextField and other controls in the Vermont Cocoa Recipe's sample code
(http://www.stepwise.com/Articles/General/VermontRecipes/), but none of those actions modify the contents of the text view, and I doubt it will work for my case.

Does anyone have an idea what I can do to work around this? I imagine there's some methods hiding in the General/WebView docs that I can override to get an elegant solution for this.

- Ben Jackson
