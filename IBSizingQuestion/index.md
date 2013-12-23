---
layout: page
title: IBSizingQuestion
---

hi.

I've noticed this behaviour in IB, I think Its a bug, hopefully someone could verify it?

if I have a NSBox with a NSBrowser as a subView (as a concrete example)
and  I have another view (arbitrary we're focused on the Browser here)

the box and the other view are stacked vertically, and they are very close to the edge of the window.
every view is set to resize on all four corners to maintain distance from the edge of the parent (springs on the inside of the box).

select the box and the arbitrary view and make them subviews of a SplitView.
set the splitView to resize instead of move (springs on the inside of the box).

test the interface.

everything works great, except when you drag the split view widget up to the top of the window, and then down again.
the browser, who should maintain its distance from the top and bottom of the NSBox, has now gone off the top of the VisibleRect of the NSBox.

I know I can probably fix this behaviour by hand coding a fix, but what I want to do here is:

1. find out if this behaviour is happening elsewhere
2. solicit suggestions for avoiding the problem, and get a new perspective on my situation.


btw: I put my NSBrowser into a NSBox bc I need to also put Other controls in the same space and its the only semi-reliable 
way to get the sizing/spacing behaviour I want in a split View

----

This is a well known problem with NSSplitView - wether it is a true bug or not, I don't know. To fix it, you will need to set Min/Max limits on the split view. To accomplish this, you need to add code to the Split View's delegate. For information on how to do this, see the SplitViewBasics. There is also an additional "bug" where the min/max limits do not take effect when the user resizes the window. This issue is also addressed on that page. -- RyanBates

