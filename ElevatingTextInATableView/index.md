---
layout: page
title: ElevatingTextInATableView
---

http://catsdorule.torpedobird.com/tmp/Cuisine.png

I'm attempting to copy the address book's GUI and improve on it, however I am having one small problem, I can't seem to get the text in the table to elevate 2px so that everything is centered.

I'd like to do this without making a custom cell.

Thanks.

----

It looks like you're returning an NSAttributedString for your objectValue method. There is a NSBaselineOffsetAttributeName which you can use to adjust the vertical positioning of your text.

----

Perfect, that was it.

