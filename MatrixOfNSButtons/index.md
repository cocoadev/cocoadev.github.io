---
layout: page
title: MatrixOfNSButtons
---



When making an NSMatrix of NSButtons, can you alter the size of the buttons?
Whenever I alter the size of the Matrix, by option pulling on the sizing handles, the buttons themselves resize.

What I want to do is keep the buttons a fixed size while enlarging the Matrix so that there is more space between the buttons.

Possible?

----
Try not holding down option?

----
When I hold down option, i spawn more button in the matrix.  Without the option held down the buttons resize along with the matrix.

----

I don't believe it's possible to do what you're asking by dragging in the GUI. Look up NSMatrix's     - (void)setIntercellSpacing:(NSSize)aSize method.
----
Really.
Seems odd you couldn't.  I"m trying to make a matrix of buttons and and a matrix of custom views (to display a little square of color).  I want to arrange them in IB so that each button has a custom color view right next to it.

----

My mistake - Command+drag the horizontal or vertical resize grips. This will adjust the spacing between the cells.
----
That's what I wanted.  Thanks.

But maybe I was getting ahead of myself.  I thought I could just as easily make an NSMatrix of custom views in IB as well.  I can't just option+drag like I do the buttons.  Is it not possible to lay that out in IB.  Only programmatically?

----

Are you sure you don't mean custom cells, rather than views? To my knowledge, you can only use cells with NSMatrix.

----
Yes, and that would be why option-dragging a custom view doesn't do anything, because IB has no idea what the equivalent NSCell subclass might be.

