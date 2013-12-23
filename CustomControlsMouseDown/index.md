---
layout: page
title: CustomControlsMouseDown
---

**Question: **I have just recently started working on drawing my own controls with drawRect:, and I think I've got the hang of it, except for mouseDown events. For example, I have a subclassed NSPopUpButton that I want to display different images for when the user clicks on it. I am guessing that I use mouseDown, and then tell the superclass to send its mouseDown event, while implementing my own code along with it. How would I tell the control to redraw itself with different images? Any help is much apprecitated. --LoganCollins

*Have a look at NSPopUpButtonCell and the NSCell superclass (you'll need the documentation for both). You want to be doing this in the cell, not its control view (the NSPopUpButton itself, a subclass of NSView).*

Alright, I have moved my methods around so I have a subclass of both NSPopUpButton and NSPopUpButtonCell, so that the former is set to use the latter. Now, I implemented drawWithFrame:inView: with my code from before, but now the menu doesn't show up when clicking the NSPopUpButton. Do I have to set up the menu programmatically because I subclassed it? What am I forgetting to do?

*Look for "menuForEvent" and call that from within your mouse down method, passing the mouseDown's event ... I *think* this is what you have to do but I may be wrong.*

