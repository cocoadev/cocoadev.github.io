---
layout: page
title: NSView
---

NSView inherits from: NSResponder : NSObject

Base class representing an area within a window that can draw its own contents and (due to abilities inherited from NSResponder) can handle events such as keys and clicks.

You can subclass NSView to create your own views and even make an IBPalette of them for use in InterfaceBuilder.

See if you can reproduce (or even explain) some of the OddViewBehavior that has been observed by unfortunate developers.

----

Redrawing a view. I spent a whole day invoking various redrawing methods on my NSTextField only to realize that the window has to be set to buffered in IB otherwise setNeedsDisplay doesn't work... It says Description Forthcoming

----

Documentation:

http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/Classes/NSView_Class/index.html#//apple_ref/doc/uid/TP40004149

----

A collection of questions and answers about NSView can be found at NSViewQuestions

----

Guidelines for setting up the key view loop can be found at KeyViewLoopGuidelines

