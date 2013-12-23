---
layout: page
title: NSMatrixCellAction
---

I have created an NSMatrix subclass and an NSTextFieldCell subclass which I have set as the cell type.

The problem is, that if I click (select) a cell in the NSMatrix, no action is sent -- I have set the mode to NSRadioModeMatrix. Even if I set target/action for the cell prototype, nothing is sent -- I do however receive the action method if I set it as doubleAction (and doubleclick).

Also, I do get the action message if I click one cell, move the mouse outside the NSMatrix and release it.

What could be wrong (I haven't posed the code to my subclasses because its really long, but everything should be unrelated to the setup)?

----

Did you wire this up in IB?

----

No, since I have subclass of both the text cell and matrix then I didn't find it easy to do in IB (especially since it must start with zero cells).

But I tried to experiment some in IB with normal NSMatrix and NSTextFieldCells, and I really think something is buggy. I have two matrices with exactly the same settings, wired up to the same object, and with the same prototype settings -- one emits action methods, the other one doesn't -- I have played a lot with the settings of each, and have not found anything to be reproducible :(

*ah... and now I figured out what the difference was: only the NSMatrix which have focus will emit an action method. But ehm... normally (i.e. without full keyboard access) NSMatrix can't even get focus -- what am I supposed to do?*

*and I have also found that if I place NSButtonCells in the NSMatrix then it works -- question is wether button cells overload something special, or if NSMatrix is hardcoded to recognize NSButtonCells?*

Most likely it is, as usual, totally hardcoded for what ever narrow minded purposes "Apple" foresaw when they designed it -- I found the following in some of the documentation: *The matrix and the radio buttons work together to make sure that only one button is on at a time.* So at least some sort of knowledge about button cells by NSMatrix exists.

----

Why don't you change the custom class in IB to the subclass you created?

*I don't know how to a) make it so that the IB instantiated NSMatrix have zero row/columns (and a fixed size) and b) how I set the cell class to my cell custom class.*

