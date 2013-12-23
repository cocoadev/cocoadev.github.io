---
layout: page
title: NSOutlineViewExpandableWeirdness
---

I have an NSOutlineView with multiple "roots".  Assume I have each top-level item *unexpanded*.  Then, as soon as I click the arrow to expand a single item, the column width changes.  Then, if you unexpand it, it changes back.  It seems to be the first column that actually gets resized.  When one or more root item is expanded, then the first column width gets bigger.  Any ideas as to why this is occuring?  I am able to prevent this behavior if I make each column non-resizable in Interface Builder.

----

Because the children of the item is shown in the same column, but with an indent, and it is exactly the width of this indent which gets added/removed on expansion of first/last item.

Although I have not considered if this is actually a necessary "feature". Also, there seems to be a bug with the autosaving of column widths, in that if you close the window while an item is expanded, that (current) column width is used for the non-expanded width the next time your start your program.

So if you generally close the app with columns expanded, the width will grow over time (if autosaving is enabled).

----

So is there a way to get rid of this "feature"?  It seems to me that it "shouldn't" expand if there's enough room in the first column even with the indent.

----

I doubt it. A table view do not keep track of the "required width" of each row, since e.g. a table view with 10,000 rows would need to do text layout on all of these each time the data source is changed, which is very slow (text layout in general). So it never really knows "when there's enough room".

