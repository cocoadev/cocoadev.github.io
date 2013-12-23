---
layout: page
title: MailThreadSummaryViewTable
---



I'm trying to create a table like that in Mail's thread summary view. To see an example, fire up Mail, then from the menu, choose View, Organize By Thread. I want to underline the text for the entire row (each cell in that row's columns) when the mouse hovers over it. I've tried approaching it from the cell side of things, then from the subclassed table view side, then the delegate side, and a few half-hearted attempts at combinations of the three.

I'm missing something simple, I'm sure. How would the rest of you approach this problem? Even better, have any of you done this before? Thanks for any pointers!

----

**Discussion**

My initial thought would be to subclass the tableview, implement mouse tracking, and when the mouse is over a cell, send [cell setMouseOver:YES]; to the cell. In the Cell's drawRect, draw the underline if([self mouseOver]). 

- MattBall

----

Thanks, Matt. What I ended up doing was using the NSTableViewRollover example as a template. I created a custom cell with a "shouldUnderline" attribute. and used the ...willDisplayCell... table delegate method to set the state if the row was selected. I overrode (in my table view subclass)     -highlightSelectionInClipRect: and     -_highlightColorForCell: to do nothing and return nil, respectively. I know - overriding private methods is bad. Since Apple most likely does this to prevent visible row highlighting in Mail as well, this is unlikely to change any time soon. Even if it does, it's a method that isn't likely to break the app if its implementation changes or the method is deprecated.

Funny enough, I wasted quite some time trying to figure out why I couldn't get the underline to appear. Turns out I was giving it the *italic* attribute and not the underline. The system control font (Lucida Grande) doesn't even have an italic trait, so nothing was happening (compounding my confusion).  < sigh > I must've looked at that line a hundred times and failed to notice I was trying to set the wrong attribute ...

In all (after getting the drawing right, etc.) it looks quite nice and the metaphor works very well for my particular situation.

