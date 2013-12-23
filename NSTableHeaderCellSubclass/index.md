---
layout: page
title: NSTableHeaderCellSubclass
---

Here's a problem that has been bugging me for weeks, and there is hardly any documentation on the issue. I have created a subclass of NSTableHeaderCell in which I draw my own background and text for the header, ala Delicious Library's list view. This works fine so far, but my problem comes when I realized that I need to implement the sort indicator. It seems that drawSortIndicatorWithFrame:inView:ascending:priority: is called by drawWithFrame:, but I can't figure out how to get it to work. I'm not sure where to get the ascending or priority variables. Has anyone created a subclass to draw custom headers that draw a sort indicator correctly? I don't need a custom indicator, I just need to call the default one to draw itself. --LoganCollins

