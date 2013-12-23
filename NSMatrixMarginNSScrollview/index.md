---
layout: page
title: NSMatrixMarginNSScrollview
---


I have a subclassed NSMatrix placed in a NSScrollView in IB. It has one row and I add columns (NSImageCell) at runtime. No matter what I have tried in IB and programatically I cannot adjust the x/y point where the columns begin. It starts at at the upper left of the view. I would like to offset it by a bit giving it some margin/space on the top and left. setIntercellSpacing is giving me the space I need between cells, but not around the edges of the scroll view. Please provide or point me in the right direction. Thanks, David
----
In IB, select the matrix.
Use the Layout->Make Subviews Of->Custom View menu.

Your matrix is now a sub-view of anordinary NSView.  You can now set the frame rectange of the matrix to any value you want.  To offset the matrix origin within its parent view, just ste its origin to something other than 0,0.
----
That is what I have done (well, sub-view of NSScrollView which I need). The x,y setting in IB for my matrix is disabled for entry, it displays values but I cannot change them. I did setBoundsOrigin: on the matrix and it has no effect.
----
You need the extra layer provided by the NSView that acts a super view for your matrix.  If you embed the matrix as the document view of the scroll view, the intermediate clip view will keep the matrix origin aligned with the clip view's origin when fully scrolled so the origin is visible.  That is what clip view does.  That is part of its purpose.  Otherwise, scrolling would not work correctly.  You will notice a similar effect with NSBox.  It too controls the frame of embedded views.

You need an extra NSView above your matrix in the hierarchy so that the clip view can control the extra view's frame thus letting you manipulate the matrix frame.

See MarginView at http://www.cocoadev.com/index.pl?CenteringInsideNSScrollView.
----
Thanks a million, that led me in the right direction.

