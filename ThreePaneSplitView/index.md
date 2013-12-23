---
layout: page
title: ThreePaneSplitView
---

I'm creating an interface where there are two custom views and an General/NSTextView in a horizontal split view (they are stacked from top to bottom). The views are topView, middleView, bottomView (descriptive, huh?). The top and bottom views are added and removed to the split view by user choice (General/BOOLs: showTopView, showBottomView). The middle view, a text editor, is always there.

I'm having trouble with the resizing ... For instance, if I resize topView, I want bottomView to stay put and vice versa. I want middleView (the text editor) to resize vertically while the top and bottom views stay the same size, even through window resize. I also need everything to work whether or not the top or bottom views are present (in other words, showTopView is set to NO and the view is removed from the split view).

In simplest terms, I want middleView to be the 'squishy' view; the only view that stretches or shrinks due to topView, bottomView, or the entire split view being resized ... if that helps at all.

I've been successful with a much simpler vertical split view (with a left view that stays put while the right view resizes with the split view). This, on the other hand, is melting my brain. Any help would be appreciated.

----
