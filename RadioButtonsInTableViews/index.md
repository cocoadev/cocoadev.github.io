---
layout: page
title: RadioButtonsInTableViews
---

Has anyone had experience using Radio Buttons in a table column? I am constructing a question/answer paradigm, specifically, I have the questions table, then for each question I have a table of objects that have a column for answer title and a column for "correct", the radio button will be used to choose the correct answer. I use bindings with CoreData, and I can't seem to figure out how to get it working with the radio buttons. I just want the items to either have YES or NO for their "correct" attribute. Is this the wrong approach or is there something I'm missing? --LoganCollins

*Why not use an NSMatrix of radio buttons?*

Because I need the ability to add and remove answers on the fly, as many as the user needs. Hence the tableview. The radio buttons are NSButtonCells taken from the IB palette (the checkbox one) and just changed into Radio buttons.

*Right, but can't you add rows to a matrix dynamically?  It just seems like a matrix is more suited to this than a table.  But I have no direct answer to your question -- never used radio buttons in a table view.*

When I was planning things out I just figured it would be easier to have it in a table instead of putting an NSMatrix in an NSScrollView and doing everything programmatically. I'll look into NSMatrix, too.

