---
layout: page
title: NSBrowser
---

Browsers display lists of data and allow users to select items from the list. They can hold one-dimensional lists or hierarchically organized lists of data such as directory paths. Browsers display these hierarchical levels in columns, which users can navigate using buttons or scrollers. An entry in a column can be either a leaf node or a branch node. Leaf nodes terminate a path; branch nodes, which have a right-arrow icon, lead into the next level in the hierarchy.  -- JimCath

[Topic]

Cool, the new NSBrowser in Panther is finally as good as it is in the Finder. Yay! Smooth scrolling, resizable columns... yay!

----
I am trying to reload an individual row of a column, and my current method of doing so is setting loaded=NO on the cell returned by loadedCellAtRow, then calling loadedCellAtRow again to reload the cell. I don't want to have to reload the entire column (that scrolls the column to top). Is there a better way for doing this?  -- ACoolie

