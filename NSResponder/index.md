---
layout: page
title: NSResponder
---

**NSResponder** -- NSResponder inherits from NSObject. Responders have the ability to handle NSEvent<nowiki/>s like mouseDown: and keyDown.

See: http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/Classes/NSResponder_Class/Reference/Reference.html

----
**Related Topics:**

----
[Topic]

----



----


One of the methods declared by **NSResponder** is **_cancelKey:**. **interpretKeyEvents:** converts ESC to a **_cancelKey:** action in the same way it converts the up-arrow to the **moveUp:** action.

Panther introduces **cancelOperation:**, a public responder method that does the same thing.

----

If you want to customize the handling of selectors, perhaps to pass them on to a delegate, it is not enough to override **tryToPerform:with:** and **doCommand:**. You must also override **respondsToSelector:** and **performSelector:withObject:**. NSApplication's **sendAction:to:from:** uses the latter two to follow the responder chain, rather than the former two. --DustinVoss

