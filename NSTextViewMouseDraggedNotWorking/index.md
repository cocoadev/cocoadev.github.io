---
layout: page
title: NSTextViewMouseDraggedNotWorking
---

6/12/03 -- 10:45amEST

Hi - I'm designing a document-based application w/ a custom subclass of NSTextView for texthandling.  I'd like to be able to show the character index for the current mouse position at the bottom of the screen when a user is dragging the mouse to select text, but the mouseDragged event handler (inherited from NSResponder) never responds to the dragged mouse.  I can get all of the other Views on the screen (the scrollbar, buttons, etc) to respond to mouseDragged, but not the custom NSTextView.  Anyone know if this is a known bug?

S

