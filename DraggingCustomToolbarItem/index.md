---
layout: page
title: DraggingCustomToolbarItem
---

The standard NSToolbar in OS X allows the user to Command-Drag toolbar items in the toolbar without opening the Customize Toolbar sheet.

My problem is that I have created a custom NSView to be used as a toolbar item so that I can intercept the mouseDown events and popup a menu. This all works fine except that when I Command-Click to move one of these items the mouseDown goes to my custom view and I don't know where to send it so that the toolbar item can be dragged by the user.

Did I miss something ovbious here?

----

Check out Apple's ToolbarSample example on your system. It has a custom view for an item that lets you drag.

----
Separate reply here -- also consider whether or not your custom view's     -mouseDown: method appropriately calls *super's*     -mouseDown: method at some point. I've never had the trouble you're having, yet every one of my shipping apps has at least one toolbar item with a custom view. The only thing I can think of is that your mouseDown method is "swallowing" the NSEvent instead of passing it on. Note that whether you call super and *then* do your own thing or you do your own thing first, then call super depends entirely on what you're trying to accomplish.

UPDATE: It looks like the problem was that I was sticking a subclass of an NSView up in the toolbar. When I changed it to a subclass of an NSControl the behavior stayed the same and now I can Command-Drag the item.

Thanks for the help.

