---
layout: page
title: NSSplitViewBug
---



Well, as long as this page is already here..

Suppose you have an NSSplitView with at least three subviews, and the first and the last are collapsed.

*programmatically set the height (width if that's the direction of the split) of all subviews to zero
*call adjustSubviews

The subviews do not resize correctly.  adjustSubviews is supposed to tile the subviews to fill the NSSplitView but it screws up in this case.

This is already submitted to Apple.

----

Are you 100 percent sure that you have the proper connections? It is really easy to adjust the wrong subviews programmatically. Make sure that you are communicating with the corrent subviews if any delegate methods are participating in tiling behavior. It is really easy to use the document view of a scroll view when you are dealing with table views, outline views or text views. Split view delegate methods should be communicating with the scroll views of these views (table, outline, text). 

----

Well, pretty sure.  I made a very small demo app (with source) for Apple, see what you think.  http://homepage.mac.com/kenferry/media/AdjustSubviewsBug.sitx

KenFerry

----
----

I seem to be having a problem with some nested split views.  I have a vertical split view,     outerSplitView, which is vertical and houses another split view on the top and a custom view on the bottom.  This nested split view I call     innerSplitView.  There is nothing special about these, just created from IB.  No delegates or anything.  

The documentation for NSSplitView says that when a split view is being resized (innerSplitView in this case), it's subviews are resized in proportion to each other.  However, when shrinking my innerSplitView, the lower item shrinks much more slowly than the top.  When expanding, behavior looks to be normal.  (Views expand 50/50 with each other).

Has anyone else experienced this?

----

Definitely have experienced that and its quite annoying. I have found no fix for it.

----

Interesting! I'm seeing it too, on non-nested split views as well, and even in my KFSplitView class ([http://homepage.mac.com/kenferry/software.html#KFSplitView]) where I obviously know the implementation.  I think it's a cumulative rounding error issue.  Each time NSSplitView resizes its subviews it dumps any extra pixels that come from rounding error into the last subview.  If you're doing something like a drag, this adds up to noticeable favoritism.  

You oughta be able to workaround any error of this sort by implementing     resizeSubviewsWithOldSize: in the delegate.  If I can nail this one I'll fix the behavior in KFSplitView, and you can either use my     adjustSubviews method for your      resizeSubviewsWithOldSize: or just use my class.

-KenFerry

----
----

An alternative which should address most of these complaints is RBSplitView.

----

Most "bugs" in NSSplitView can be "fixed" if you implement the right amount of delegate methods correctly. See SplitViewBasics, or use RBSplitView or KFSplitView.

