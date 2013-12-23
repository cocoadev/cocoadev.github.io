---
layout: page
title: ControllingSplitPanes
---

**This is no longer relevant. The resizing is properly constrained.**

Hi,

I have a split pane in a drawer, and I have set up a delegate so that `- (float)splitView:(NSSplitView *)sender constrainMinCoordinate:(float)proposedMin ofSubviewAt:(int)offset` is called, and I can constrain the maximum size of the lower pane.

However, because my splitview is in a drawer, when the user reseizes the main window, the whole drawer can grow, and the splitview grows accordingly, this has the effect of making the lower half of the splitview larger than its normal maximum size I give it via the above delegate method.

How can I ensure a splitview pane is kept to a maximum size during the main window resizing? 

Thanks very much for any help, it is truly appreciated,

-Peter

----

How about implementing the **drawerWillResizeContents:toSize:** or **splitView:resizeSubviewsWithOldSize:** delegate method and explicity re-constraining the split view? I know it's a pain. I, for one, am not happy with Cocoa's auto-resizing mechanisms. �DustinVoss

----

Thanks for the input... I will try this. But for the record I have decided to re-engineer the interface so that this is not a problem... it was becoming too messy, and some of the reason for this was not being able to neatly keep things in place in a complex interface easily.

----

It should be noted that this problem seems to be pretty easily overlooked, as XCode editor windows' drawers can have the exact same problem when the file layout in said drawer is split, and then the window, or the split view is resized.

----

I'll just chime in that Dustin is totally right, and it shouldn't be hard at all to implement this in **splitView:resizeSubviewsWithOldSize:**.  Just call *adjustSubviews* to get the usual resizing behavior, then fix anything you don't like.

*I usually am... :) �Dustin*

