---
layout: page
title: FirstResponderOnMouseDown
---



Okay, my problem is quite simple. I have two views. If, when I click on either view, it is not FirstResponder, then I have to click it twice to make it execute its mouseDown method. The first click to make it firstResponder, and the second one to actually perform the mouseDown method. What's the solution to avoid double-clicking the view ?

-- Trax

----

return YES to     -(BOOL)acceptsFirstMouse:(NSEvent *)event

From [http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/ObjC_classic/Classes/NSView.html#//apple_ref/doc/uid/20000014/BCIDIGDA]

*Overridden by subclasses to return YES if the receiver should be sent a mouseDown: message for theEvent, an initial mouse-down event over the receiver in its window, NO if not. The receiver can either return a value unconditionally, or use the location of theEvent to determine whether or not it wants the event. NSView�s implementation ignores theEvent and returns NO.*

