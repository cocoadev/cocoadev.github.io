---
layout: page
title: RoundedRectRowHighlighting
---

 Anyone know how to implement custom row highlighting using RoundedRectangles?
I have the BezierPath sorted with:
 [[NSBezierPath bezierPathWithRoundedRect:[self rectOfRow:row] cornerRadius:50] fill];

But now I just need to get it in a tableview and override the default row highlighting with drawRow:(int)row clipRect:(NSRect)rec
Any help, tips, or examples for doing this? Its being used alot more recently, but there doesn't seem to be much information about how todo it.

----

I'd subclass a cell and override highlight:withFrame:inView: then make the NSTableColumn(s) use the subclass as the dataCell.

----

Take a look at OAGradientTableView in OmniAppKit. That will show you which NSTableView methods you need to override in your subclass. Then, use the RoundedRectangles category discussed on this site.

----

I looked at OAGradientTableView and it is not so clear which methods need to be overridden.  :-(

----

I think the following are sufficient:

- (id)_highlightColorForCell:(NSCell *)cell;

- (void)highlightSelectionInClipRect:(NSRect)rect;

_highlightColorForCell: should return nil, so the table doesn't draw the normal selection, and you can override highlightSelectionInClipRect to draw the selection however you like.

----

there is a solution for replacing the ugly black rect highlights when dragging over a row with nicer, iTunes-style highlights in UglyBlackHighlightRectWhenDraggingToNSTableView

