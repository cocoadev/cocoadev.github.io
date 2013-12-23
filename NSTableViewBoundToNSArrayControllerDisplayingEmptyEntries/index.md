---
layout: page
title: NSTableViewBoundToNSArrayControllerDisplayingEmptyEntries
---

Greetings, Everyone!

I'm having some difficulties using Panther's Controller layer with NSTableViews. I have a window with a table view in it, and an NSArray instance variable in the NSWindowController class. I have an NSArrayController instance accessing my instance variable, and I have bound the NSTableView to the NSArrayController via the content and selection indexes keys. It correctly loads the objects, however, when they are displayed, they appear empty. I can select these empty rows, but they appear nonexistent when deselected. It just looks like there is no text in the cell, even though the cell exists.  I have verified the existence of my strings in the instance variable, and all objects in the array instance variable are NSStrings, so I don't understand why this would be. Has anyone experienced similar difficulties, or can anyone offer a suggestion?

Thanks a bunch,
-- EliotSimcoe

----

Instead of linking the NSArrayController to an array instance variable, specify the object class that the array is going to hold (using IB Attributes). The controller will create and manage the array for you. After you specify the class, specify the class keys (variables/methods) so the controller knows how to access that class. After the keys are set you can set up each table column to a different key. Here's a tutorial which I suggest you take a look at:

http://developer.apple.com/documentation/Cocoa/Conceptual/ControllerLayer/Tasks/ccwithbindingsplus.html

Note: This tutorial is building on a previous tutorial which can be found here:

http://developer.apple.com/documentation/Cocoa/Conceptual/ControllerLayer/index.html

Hope that helps.

-- RyanBates

----

Typically, one would not bind the NSTableView to the NSArrayController directly. One would bind the "value" key of each NSTableColumn to the appropriate model path of the NSArrayController. For example, bind NSTableColumn's "value" binding to the NSArrayController's "name" model key path. �DustinVoss

----

To fix this problem, simply go into your tableColumns binding, and in the Null Placeholder, type something like (no title) or (untitled) or something, that will be displayed instead of the empty blank entries.

