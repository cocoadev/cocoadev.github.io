---
layout: page
title: ScrollRectToVisible
---

I've got a custom view that draws pretty round rects (via NSBezierPath) of varying height with controls in them. Using     [self scrollRectToVisible:selectedRoundRect]; (where selectedRoundRect is the round rectangle control a user is moving to via up/down arrow), it works fine if I'm scrolling to a rectangle "higher" in the view (toward the max y in a non-flipped view), but doesn't do a thing if I move to a "lower" view (closer to y=0). Of course, I'm talking about rects that are beyond the visible rect of the clip view. 

Why is this? What am I incorrectly ASS-uming? ;-)

----

Never mind. Turns out I was telling the view containing the individual roundrect (and its controls) to     scrollRectToVisible: but I should've told its superview (the custom view I mentioned) to do the scrolling. Apparently, it doesn't climb the hierarchy? I interpreted the docs to mean that it did ... Oh well.

*Glad you've sorted it :) Retired.*

----

But I still think this is a common problem. If you want to scroll a subview to visible you can also do this using
    [self scrollRectToVisible:[self bounds]]
which looks somewhat prettier than
    self superview] scrollRectToVisible:[self frame

