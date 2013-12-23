---
layout: page
title: CustomNSCellLikeInNewsFire
---



I'm trying to figure out how they created the cell in the tableview on the left hand side in news fire.  It has a title in black, a sub title below it in light grey and a badge on the right with the number of unread articles for that feed.  Any help would be much appreciated.

----

For the shading, see ITunesStyleGradient.

For the badge, you'll want to either create your own custom cell that draws the badge how you want it, or create a new column (setting an NSImageCell as its prototype cell),   render an image using a pre-made badge background and draw a number in it, then set that as the image used in the image column.

