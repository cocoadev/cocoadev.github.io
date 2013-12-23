---
layout: page
title: SplitViewSizeRestrictions
---

Hello!

Let's say I have two Table views in a split view. They are proportioned 30/70% so one table is a bit wider than the other.

How can I restrict it so that when the "knob" is being used to alter the width of the split, the minimum width any of them can get is say 10% or X pixels.

I see this in the Finder view and if you force the "knob" the view is hidden.

Thanks

----

Look at NSSplitView's delegate methods, particularly these two:
    
- (float)splitView:(NSSplitView *)sender constrainMinCoordinate:(float)proposedCoord ofSubviewAt:(int)offset;
- (float)splitView:(NSSplitView *)sender constrainMaxCoordinate:(float)proposedCoord ofSubviewAt:(int)offset;

