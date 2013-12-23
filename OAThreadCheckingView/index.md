---
layout: page
title: OAThreadCheckingView
---



This is a useful class that you will never use directly, but might help debug problems related to multithreading. Recall that AppKit isn't thread safe, so you have to be sure you're only drawing from the main thread... IF <code>OMNI_ASSERTIONS_ON</code> is defined, OAThreadCheckingView will pose as NSView and check assertions every time any view calls <code> lockFocus, setNeedsDisplay, </code> or <code>setNeedsDisplayInRect</code> to be sure that the call is coming from the main thread.

