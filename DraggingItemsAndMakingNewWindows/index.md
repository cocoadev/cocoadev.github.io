---
layout: page
title: DraggingItemsAndMakingNewWindows
---

I want to be able to drag objects from my main window and create a new window for them if they are dropped outside
of the main window.  I want to accomplish a TabView with draggable tabs.  Tabs can be ripped from the view and dragged out 
of the main window and create their own.  You can also drag the windows back into the tab view to remake the tabs.  This is the same behavior as InterfaceBuilder when dragging an inital window object from the palette, so I know it is possible. Does anyone know what object would have to respond to dragging messages to get this behavior to work?

-PJ

----

This stood unaddressed in CocoaDiscussions since Sep 2004. Perhaps someone sees the potential for an interesting discussion of an IB-clonish interface design.  At any rate, the OP has undoubtedly moved on to other, more pressing matters, as [s]he never elaborated the idea.

----

I recommend anyone interested in this looks at the source code for Adium, as their message window has draggable tabs which can form new windows. Please note that Adium is GPL licensed.

----

Use draggedImage from NSDraggingSource to find out the finishing point of the drag, if ends outside window then make window...

