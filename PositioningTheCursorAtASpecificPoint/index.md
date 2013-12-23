---
layout: page
title: PositioningTheCursorAtASpecificPoint
---

Does anyone know how to set the location of the mouse? I am attempting to make resize code as accurate as possible so i must place the cursor at a specific point at the start of the resize.

Thanks.

----

Controlling the Mouse Cursor

http://developer.apple.com/documentation/GraphicsImaging/Reference/Quartz_Services_Ref/Reference/reference.html#//apple_ref/c/func/CGDisplayMoveCursorToPoint

----

Followup, the code I used to position the cursor is the following:
    
CGDisplayMoveCursorToPoint(CGDisplayPrimaryDisplay(NULL),(CGPoint){4,4});


----

Out of curiosity, won't this (as is) potentially fail (not fail, but cause erratic cursor behavior); depending on how the user has their displays setup?

----

Do cursors no longer have any concept of hot points, like they did back in the ResEdit days? It would seem that such a concept would be there precisely to make sure that things will line up properly...

----

Why not look at the documentation? NSCursor has a method     -initWithImage:hotSpot:

----

I think the original poster is going to try to resize a window by programmatically moving and clicking the mouse. Oh dear.

----

... or he's trying to resize something in a drawing view. Either way, this is a horribly overcomplicated way of going about it. I recommend he re-read the related documentation.

