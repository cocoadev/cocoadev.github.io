---
layout: page
title: FixedDistanceBetweenViewsWhenResizing
---

On the left side of my window, I have an NSTableView that I want to resize with the window, so I set its horizontal and right-side autosize controls to spring and its left-side control to fixed-struct.

Immediately to its right, I want some other stuff (for now, just some text!).  I'd like the new view to be fixed to be the appropriate distance (18 pixels, according to the Aqua guides in IB) away from the NSTableView.  But no combination of the autosizing controls seems to be able to do this: they only let me control the distance between the new view and the edge of its enclosing view, but I want it to be based on the edge of the NSTableView next to it.  Do I need to put some more levels of hierarchy in somehow?  Or is this really uncommon enough that I need to somehow handle it in code?  --DavidGlasser

----

IB springs are really messy; I just had a similar problem. I think in this case the solution is to set the NSTableView to have a spring for horizontal, but not the right-side. Then set everything (and I mean EVERYTHING) to the right of the table to have a single spring in its left-side control. If you still can't get it to work right, try putting everything to the right in a borderless, titleless NSBox (some menu option somewhere allows you to do this without breaking anything). Then set the NSBox to have a single spring in its left-hand side. You might not be able to get this ever to work (I didn't, not really), but it helps. NOTE: untested for your particular situation. --JediKnil

----

I'll give these a shot and report back.  If these end up not working, what do I do?  Stick them in an NSBox and give that NSBox a resizeWithOldSuperviewSize? --DavidGlasser

----

Aha, your first suggestion works perfectly!  I only understand it now that I've discovered the "documentation" for the springs, which is the setAutoresizingMask: method of NSView.  Basically, what having both the horizontal and rightside springs set means is that if the window becomes ten pixels wider, the view becomes five pixels wider, splitting the extra ten pixels halfway between the width and the wide margin.  Having only the horizontal spring set means that when the window becomes ten pixels wider, so does the view; then setting my second column of stuff to having a left-hand spring means that it will move over by ten pixels.

I guess before I was just thinking that the springs do "something cool by magic" without actually thinking about what they really did. --DavidGlasser

