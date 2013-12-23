---
layout: page
title: NSCellsInNSCell
---

Is there a good tutorial showing how to subclass NSCell to create a cell with more than one NSCell inside of it?

I need an NSCell that displays a multiline block of text at the bottom, with a combobox or popupbutton at the top. Under some circumstances, the popup/combo won't be displayed. The textblock needs to be editable, in a Core Data bindings environment. Cell will be contained in an NSOutlineView.

-- dd

----

Cells can't contain other cells. You may need to re-think your design.

----

Certainly they can, you just have to do all the work yourself. When your master cell is asked to draw, it must ask the subcells to draw. When an event comes in, it must redirect the event to the appropriate subcell, and so forth.

----

well, on the assumption that cells can contain other cells, that sounds like the best way to get existing functionality in a combined cell. 

Is it as simple as instantiating a bunch of NSCell of the required types  and then.... what? do I set up a bounding rectangle as the bounds for the contained cell? Is there more to it than that? The thing least clear in my mind is how the various cells get physically located in the cell that they are in. I'm assuming that that's all just stuff I have to do.... right? Any examples anywhere? Are there any Core Data issues? I assume there are methods that the containing cell would override to direct the appropriate information to the subcells it contains? Which are these?

--dd

