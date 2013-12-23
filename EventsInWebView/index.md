---
layout: page
title: EventsInWebView
---

Hello, I'm currently using the Safari Webkit in a Cocoa program.  I have an NSView to which I have added a Webview.  I want to be able to listen for mouseDown and keyPress events on my NSView, but for some reason they never get passed down to it when the user clicks or types on the WebView.   Does anybody have any idea how I can get these events?
----
Just look through the documentation on WebKit.
----
I managed to get mouseDown Events on the WebView by using poseAsClass on NSWindow and making a subclass that forwards mouse down events to the WebView container where I was listening, then calling sendEvent on the superclass.  I found no way in the documentation for WebKit to get mouse events working.

