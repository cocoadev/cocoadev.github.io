---
layout: page
title: HowToMakeNumberColumnLikeMailAndItunes
---

I am new to this and I have tried to search the site with no luck.

What I wonder is how you can make a column in a list view have a bordered/colored background like the counter in Mail 's Mailbox list.
iTunes also has this for when you are downloading an item.

Thanks..

----
I don't think it's a separate column with a (rounded) gradient background. These list views probably use custom NSCell's that also display a count aligned to the right.

Drawing a custom cell should be easy. See http://www.martinkahr.com/2006/11/04/data-for-a-custom-cell-in-a-nstableview/ for some strategies on how to get data in to the cell (in this case the normal text and the count). -- JorisKluivers

----

Thank you., I was more interested in the column that shows the mail count for instance, it seems to have a grey(er) background.
Similarly in Mail you can color an entire line and it has these nice rounded edges.

----
Ah, I automatically assumed you were referring to the following:
http://bp2.blogger.com/_tjRYYhTSYFY/RYcgCPT1yNI/AAAAAAAAAAY/EwwUI9PkdK4/s1600-h/Picture+4.png

----

That's the one.. :) thanks.

