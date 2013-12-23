---
layout: page
title: FlickeringScollerBug
---



When I resize a window such that the scrollview becomes too small for the contained view, the vertical (or maybe horizontal too) scroller flickers briefly. It is as if the scrollview is unsure of whether the contained view is too big for the new size. I think the scroller flickers for the vertical height of one row of the contained tableview, and the scrollers also have to be hidden for the flicker to occur.

As an example, it occurs in jumsoft's new app (it's in the bottom left tableview):
http://jumsoft.com/relationship/

Does anyone have any insight or a solution to this problem?
----

When autohiding the scrollers, make sure to set the NSScrollView to have both a vertical AND a horizontal scroller (via setHasHorizontalScroller:YES). Although the horizontal scroller is never shown, this eliminates the flickering vertical scroller bug.

