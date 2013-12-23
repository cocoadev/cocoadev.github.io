---
layout: page
title: NSCell
---

Reference: http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/Classes/NSCell_Class/Reference/NSCell.html

Controls & Cells: http://developer.apple.com/documentation/Cocoa/Conceptual/ControlCell/index.html

----

NSCell is a class for taking the weight off certain controls that use lots of similar parts, like matrices and table views. They draw things, but aren't views (the controls are views). The view/control takes care of view responsibilities other than drawing, of which there are many. So this can save a lot of overhead in controls with many subcontrols.

The way cells work is not completely clear in all respects, at least if you try to use one in ways it was not necessarily intended, like in your own custom control.

This could use some filling in if anybody has any knowledge of cells that isn't covered in the docs.

----
**F.A.Qs**
----

**What is the primary difference between views and cells?**

Cells are very light weight compared to views and therefore are preferred over views when used in large quantities (I would say over 20). However, unlike views, cells cannot display themselves and must be drawn inside a view designed to contain cells (usually a subclass of NSControl).

----

**Do cells handle the drawing?**

Yes, the cells do most of the drawing unless you want to draw something outside of the cells (such as lines between the cells in an NSTableView). Usually the control simply acts as a container defining where and how to draw the cells (position, size, etc.).

----

**Several controls, such as NSButton and NSTextField, have only one cell. Why does it need a cell? Why not have the control manage its own contents?**

There are several controls (NSTableView, NSMatrix, NSOutlineView, etc.) which are not designed to hold other views - only cells. Therefore, if you want the ability to place a control in a table view or matrix, you will need to put the "guts" of the control in a cell. Basically, using a cell inside a control gives you more flexibility in the future.

----

**Should every control have a cell?**

As stated in the previous answer, controls without cells are less flexible and cannot be easily placed inside a table view or matrix. Several controls, such as the NSProgressIndicator and NSColorWell do not use cells and are therefore much more difficult to place in a table view or matrix.

----

**Can a cell contain a view?**

Only views can contain other views. But the cell is (at least most of the time) contained within such a view, so it could add to that view.

This is what happening when editing an NSTextFieldCell, then it will add the field editor to its controlView (which could be an NSTableView, NSTextField, or something third).

----

**Can a cell contain another cell?**

It can at least wrap for another cell -- i.e. one can write a dummy cell class with two instnace variables pointing to other cells, and then, in every cell method of interest, our dummy cell just passes on the message to the two other cells (preferably adjusting the frame rectangle, so that they do not overlap).

----

**If I want to customize a control, how do I know what to subclass? The control, the cell or both?**

That would depend on the control, but often it should probably be enough to subclass the cell.  Though just as often you may find, that it's not that easy to customize and existing control.

----

**Should every cell that handles events be a subclass of NSActionCell?**

The documentation says: *an NSActionCell does three things: it usually performs display of text or an icon; it provides the NSControl with a target and an action; and it handles mouse (cursor) tracking by properly highlighting its area and sending action messages to its target based on cursor movement*. Though it's not easy to spot exactly how the NSActionCell alone does these things (but I do believe it extends the instance data to contain the target/action pair).

----

**Can a single cell contain several buttons? The closet example in the real world is the "My Rating" column in iTunes, where each of the five stars sort of acts like a button. I need more traditional, textual buttons, though.**
----
Yes.  See http://www.stepwise.com/Articles/Technical/NSCell.html  (link is dead, site is defunct)

