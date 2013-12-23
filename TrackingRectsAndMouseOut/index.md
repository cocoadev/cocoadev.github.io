---
layout: page
title: TrackingRectsAndMouseOut
---

I have an General/NSView that basically draws a rectangle. At the moment, I have a tracking rect on one edge of the rectangle and I use this to detect when the mouse is near the right edge of the rectangle. When it is in the tracking rect ( �3 pixels of the edge - hopefully a good amount) the rectangle changes color so that it shows it can be manipulated by the mouse. When the mouse exits the tracking rect the rectangle goes back to its original color. So far so good :)

The edge of my rectangle can be dragged to extend/shrink it (really basic) but the problem occurs when the user drags the edge of the rectangle. As he/she drags the mouse,it leaves the tracking area, but because this is during a drag operation, mouseExited never gets called, and so it is possible for the rectangle to be resized, and the mouse released occurs outside the tracking rect. To still have my rectangle in the "yeah-sure-you-can-drag-me" color. This is then not rectified until the NEXT mouseExited operation, which involves entering the tracking rect and then leaving it again.

Is there any way I can check for mouse exited AS I drag?

- Peter

----


I don't entirely understand what you're trying to do here, but it seems to me that you wouldn't really want it updated as you drag, just to be the correct color when you finish the drag.  The easiest way to handle that IMHO would be to implement     -mouseUp: and just set the rectangle to the correct color in that method.  -- Bo

----

This doesn't quite work either.

Basically the problem boils down to the fact that I want the right edge of my rectangle to be draggable, so the rectangle can be made longer or shorter, but only along the x axis. The only problem is that during the drag operation, it's not kind to force the user to keep the mouse over the in line vertically with the rectangle - this sounds complex - perhaps a better way of explaining would be to look at any standard scroll bar - you must put the mouse over the scrollbar to initiate the scroll, but once initiated the mouse is free to leave the area on screen that is occupied by the bar itself, yet the scroll bar still tracks the scrolling. 

This is great and my rectangle does this at the moment no problem. When the above DOES occur, no mouseExited event gets called (and so I can't set the rectangle back to its original color) - in essence, during the drag the mouse manages to leave my tracking rect without being noticed. Cheeky!

Setting the color of my rectangle back to normal on mouseUp as suggested unfortunately isn't an option either - consider that the user is a fairly precise dragger and keeps the mouse within the tracking rect (hot draggable area of my rectangle) all the way through the drag, well, in this scenario, even though the mouse is STILL in the tracking rect (and so my rectangle should show it is active by still being drawn in the color that indicates it is manipulatable) the color of my rect will be set back to normal when it should not.

If code would help demonstrate I will post what I have so far of my little project.

This one has me stumped so any help is much appreciated.

----

I didn't say set it to the "normal" color; I said set it to the "correct" color.  I was thinking more along the lines of checking the mouse position on mouse up, and if it's outside your tracking rect, set it to the normal color, and if it's still in the tracking rect, leave it as the highlighted color.  -- Bo
