---
layout: page
title: NSOutlineViewPersistence
---

*Note: I posted this to the Cocoa-dev list, and I'll add any insight I get there to this page.*

I have a feeling that this will be more of a design question than anything:

How do I refresh the data source for an NSOutlineView and keep the expanded/collapsed state?

Here's the situation: I have a routine that scans the text in an NSTextView on a timer, and builds a tree out of lines that start with \section or \subsection.
At the moment, it's represented as nested NSMutableArrays. ( a detail, the 0th element in each array is a dictionary that has the text of the line to use as the objectvalue in the outline view)

Every time the contents are re-scanned, the root array is emptied and the tree is rebuilt with new objects, which end up being equivalent to the old objects in the old tree up to the point just beyond an edit (because the text range is stored in the aforementioned dictionary).

I don't implement persistentobjectforitem or its counterpart. (because I don't understand what they do and online references confuse me)

What happens: on a refresh (triggered by an edit in the text view) the outline view stays the same except that the item nearest to the edit is collapsed, all the way up to its parent, and so is everything after it.

What I want: the outline should only change if the structure changed (ie, if the number of lines that had \section, etc changed or their titles changed) and I don't want anything to collapse without user intervention.

Help.

Questions:

How does NSOutlineView handle changes in the datasource with respect to expanded/collapsed status?

----

It looks like this was never answered.  I had the same problem, and found this, which works for me, in the documentation for NSOutlineView:

    
[outlineView setAutosaveExpandedItems: YES];


----

In order for setAutosaveExpandedItems:YES  to work you must also implement outlineView:itemForPersistentObject: and outlineView:persistentObjectForItem:  as well as setting an autosaveName. 

All this info is of course found in the docs for NSOutlineView.

