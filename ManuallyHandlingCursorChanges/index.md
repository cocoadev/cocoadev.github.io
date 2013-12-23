---
layout: page
title: ManuallyHandlingCursorChanges
---



I have a view with some rather complex rules about when to show which cursor, and the cursor rectangles are not only rectangles, and they are sort of "stacked".

So using     addCursorRect:cursor: seems to complicate things quite a bit (because I need to write my own region stuff to provide the proper rectangles, and I need to reset the rectangles on various events).

So is there any way to go about simply handling this manually? would I just overload mouseMoved: and change the cursor using NSCursor here? How well will this play with other views (I also need to change cursor when my view hasn't got focus)?

