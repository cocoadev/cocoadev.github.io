---
layout: page
title: HowToDisableFirstMouseClickInTableView
---

I'd like to keep the selection of a table view when I click on it to focus it, does anyone know how to do it?

Gustaf

----

I've found this for you in a page from Apple's Documentation:

One of the earliest things to consider in handling mouse-down events is whether the receiving NSView should become the first responder, which means that it will be the first candidate for subsequent key events and action messages. An NSView that handles graphic elements that the user can select�drawing shapes or text, for example�should typically accept first responder status on a mouse-down event, by overriding the acceptsFirstResponder method to return YES. This results in the window making the receiving NSView first responder with NSWindow�s makeFirstResponder: method. Some types of NSView, however, may not wish to change the selection upon the first mouse click in a non-key window, which should normally only order the window to the front. NSView�s acceptsFirstMouse: method controls whether an initial mouse click is sent to the NSView or not. By default it returns NO, which in most cases is appropriate behavior. Certain subclasses, such as controls that don�t affect the selection, override this method to return YES.

