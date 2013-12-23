---
layout: page
title: TableViewScrollingProblem
---



I have been trying to scroll a table view from code. I can change the bounds of the clip view successfully, but the header view does not update. I tried sending     tile and     reflectScrolledClipView, but these methods do not update the header view. The only thing that is working is creating a dummy scrollWheel event to force the table view to update it's scroll state. 

Does anyone know how the table view's header is told to update when the table view's clip view bounds changes?

PS - the scrolling problem is related to asking the table view to scroll horizontally by setting the clip view's bounds to a new rect.

----

I figured it out. A table view will not update it's header view if the clip view's bounds is directly modified. To get the header view to scroll along side with it's associated table view, you have to tell the table view to     scrollRectToVisible:.
