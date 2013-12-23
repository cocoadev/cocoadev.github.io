---
layout: page
title: RedrawingIssuesWithSemiTransparentViews
---




I have an NSTableView which has its background set to be semi transparent in IB.  When I add content to the table view, they appear to get drawn twice, giving a 'boldface' effect.  If I select a row (or drag something over it) it reverts back to its normal, non-bolded self.  How can I avoid these double-draws?

(screenshots attached)

http://10base-t.com/normal.jpg

normal rendering, eg after selecting the rows

http://10base-t.com/double-drawn.jpg

funky double rendering, eg after a new item has been added to the table

* The view isn't getting redrawn (since cells are more primative with drawing), call setNeedsDisplayInRect: on the cell rect.*

hmm...not sure how to do that.  I tried [self setNeedsDisplayInRect:[aCell rect]]; - which gives me 'selector not recognized' errors

* is that in your table view or in your data source/controller? * 

in the controller

"Only Views responds to setNeedsDisplayInRect:; you should send that message to the NSTableView instance, once for every cell needing to be redrawn."

at what stage would I call this?  I have tried at  willDisplayCell:

