---
layout: page
title: MainWindowAndKeyboard
---



I'm trying to make sure that my app - which has multiple windows - responds the same to keystrokes no matter what window has focus.

I've subclassed NSWindow, and in that subclass I've overridden keyDown and acceptsFirstResponder. I also tell this window to become main (makeMainWindow method).

This works fine if the window has focus, but as soon as I click in another window, the keyboard just produces the telltale 'bing' sound. How ould I go about intercepting the keystrokes at the very top of the responder chain?

Any pointers would be helpful.

----

Check out:
http://www.cocoadev.com/index.pl?KeyDownEvent

----

You can create a custom subclass of NSApplication and specify this as the application class in your Info.plist file.  Then you can override -[NSApplication sendEvent:] to handle key events as appropriate before passing them along to the superclass.

I used this technique from a CocoaJava application a few years ago to handle the key events generated by a custom input device distinctly from those that were passed along to whatever window was key.  (The input device put control characters around each sequence of keys it generated.)
