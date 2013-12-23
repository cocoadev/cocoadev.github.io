---
layout: page
title: ScrollSubViewControlsWhenCollapseExpandViewWithRBSplitView
---

With RBSplitView, how can I get the control's subviews to scroll up/down when I'm using expandWithAnimation or collapseWithAnimation? I would like a behaviour like you can find in Safari 3.0's search view (Command-F) or in Coda's search view. When the subview expands or collapses, you can see that the controls will follow the animation (up/down) instead of simply standing there.

----

Check and/or adjust the autoresizing masks for your subviews and/or controls. Recall that any subview added to RBSplitView is automatically wrapped in a RBSubSplitView. The wrapped view has its frame set to the SubSplitView frame and its autoresizing masks set to (NSViewHeightSizable | NSViewWidthSizable). 

The best results I've had using RBSplitView have come from tossing out the palette, setting a custom view to the RBSplitView class and then doing all setup and initialization in the awakeFromNib in an associated controller.

----

