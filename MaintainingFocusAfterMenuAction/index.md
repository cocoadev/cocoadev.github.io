---
layout: page
title: MaintainingFocusAfterMenuAction
---



I'm having some problems with an application I'm writing.  When the user invokes a menu action using the keyboard (or possibly the mouse) the focus does not stay on the control that was in focus before the action occurred.

I believe I've narrowed the cause down to me setting the contents of a control in the action handler.  Is there any way to maintain the focus on the control that was in focus before the menu action was invoked without removing this code?

----

Take a look at NSResponder and the Cocoa event handling guide. In particular, NSWindow's makeFirstResponder, or NSResponder's acceptsFirstResponder and resignFirstResponder, depending on what is actually happening to make your control lose key focus.

