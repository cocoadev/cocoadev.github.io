---
layout: page
title: WellMatrix
---

Hello.

I have an General/NSMatrix made up of General/NSImageCells.  I am using these cells as a sort of well, for the user to drop things.  I have two problems.

First off, I have no idea how to accept a drop in an individual image cell, determine what cell it was dropped on, and modify the appearance of that cell accordingly.

Second, I need the user to be able to select an image cell and for it to appear highlighted.  This should be easy using the built in selection mechanism of the matrix - but it is doing nothing.  I have tried all of the modes, etc.

Perhaps I should not be using a matrix?  Thanks for any help.

*You could try using General/NSButtonCell for the second problem; highlighting is a snap. As for the first problem, check out the methods     getRow:column:forPoint: and     cellAtRow:column: on General/NSMatrix. You're probably going to have to subclass General/NSMatrix and implement the methods of the General/NSDraggingDestination informal protocol. (At least, that's what I did when I had this problem, although my implementation is pretty specific to my program) --General/JediKnil*
