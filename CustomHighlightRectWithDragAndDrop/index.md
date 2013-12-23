---
layout: page
title: CustomHighlightRectWithDragAndDrop
---



How to make a custom highlighting of the drop target? For example like Address Book does when dropping contact to some group. I am subclassing the NSTableView to see which of the draw methods makes that ugly black drop destination rectangle. No luck. I can get rid of it only if I make an empty drawRect --AndreyBabak

I've had luck using different highlights by overriding drawRow and using some sketchy ways of determining the drop row - but I have no idea how to get rid of the black rect.  Any ideas, I have the exact same question as the OP.

----

Look up setKeyboardFocusRingNeedsDisplayInRect: and NSSetFocusRingStyle(), they should put you on the right track to getting the focus rings cooperating. --CurtisHoover

What does the focus ring have to do with this....?
 
Sorry guys, I miss read your post. I poked around in the source for the NSTableView, but I didnt see anything that looked like it was doing it. -- CurtisHoover

----
**I think this is an unresolved FAQ:** UglyBlackHighlightRectWhenDraggingToNSTableView

