---
layout: page
title: TableViewRedrawIssues
---

I use an General/NSTableView and I'm creating the columns on launch. The problem is that the cells all draw a background of white, when I add rows to the table. As you would guess this creates issues using a striped rows. Do I have to tell the cells to draw transparent backgrounds? Or is there something that I'm missing?
----
I just ran into the issue for the first time myself. A quick search through Apple's cocoa-dev mailing list archives revealed that this behavior seems to be a bug and can be worked around by telling your new columns data cells not to draw their backgrounds. e.g.      General/yourNewColumn dataCell] setDrawsBackground:NO]; 

Regards

-- [[NeilVanNote

----

Thanks Neil,

As a kinda temporary workaround, I had been using this sorry piece of code:
    
- (void)tableView:(General/NSTableView *)tableView 
  willDisplayCell:(id)cell 
   forTableColumn:(General/NSTableColumn *)tableColumn 
	      row:(int)row {
    if ([cell respondsToSelector:@selector(setBackgroundColor:)]) {
	if (row % 2) {
            [cell setBackgroundColor:
                  General/[NSColor colorWithCalibratedRed:0.94 green:0.95 blue:0.98 alpha:1.0]];
        } else {
            [cell setBackgroundColor:General/[NSColor whiteColor]];
        }
    }
}


----

Here's another strange one - I'm curious if anyone else can reproduce the following behavior (General/XCode 1.1 and IB 2.4 v349):

I put General/NSTableView and General/NSTextView into my document window, selected them both and issued the IB command "Make Subviews of �> General/SplitView".
The document reloads the table data by unarchiving it from a file in which it was stored as an array of objects of an General/NSObject subclass.

This is the simplest of all possible General/SplitViews - with no custom behavior whatsoever, just the automatic compensation between the two subviews.

Here's the observation:
The split view fills the document window and resizes with it, and the table view shows a "feeble" redrawing response when the  window is resized.
But it eventually redraws OK. However, I made the feebleness (feeble-osity?) go away completely simply by asking the table to draw its grid lines automatically.

The (possible) bug is the redrawing feebleness observed when the gridlines are not present.

I do not, for example, see this when the table view is a subview of, say, a tab view.
