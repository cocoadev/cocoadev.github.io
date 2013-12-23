---
layout: page
title: AddressBookStyleNSPopUpButtons
---

Does anyone know how to make an NSPopUpButton like in Address Book, where it gets a gray background when you hover the mouse over it, but it also makes the background color of the corresponding data field a lighter shade of gray?

Also, I can't figure out how change the foreground color of an NSPopUpButton's title; the setAttributedTitle: method seems to not work in NSPopUpButton.

----

One way would be to subclass NSPopUpButtonCell and draw whatever you want. I did this for the gray-color type NSPopUpButtons like in iTunes Party Shuffle and Podcasts. Use     drawBezelWithFrame:     to draw the bezel part, and if you want to redraw the text use     drawInteriorWithFrame: and draw an NSAttributedString with whatever attributes you want. --LoganCollins

----

Thanks! I know this doesn't really have much to do with NSPopUpButtonCells, but how do I draw that shape where it's just rounded on one side, and everything else is straight?

----
Those are called RoundedRectangles.

----

OK, thanks, that helped a lot!

