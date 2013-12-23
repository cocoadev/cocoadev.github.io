---
layout: page
title: WhyIsMyViewStuckInTheLowerLeftCornerOfMyScrollView
---

**Question: I want to programatically add a custom view (created  and populated with controls in IB) to an General/NSScrollView, 
but when I add my  custom view to the scroll view (using -setDocumentView: ), it remains  stuck in the bottom-left corner !**

So, I thought that overriding -isFlipped: in my custom view so that  it returns YES would solve the problem. In a way, it did: 
now my  custom view is at the right place...but all my controls are drawn inverted (controls at the top in IB are at the bottom) !!!

I can understand why (the coordinate system is flipped in my custom view), what I don't get is  how can I have a custom view 
created in IB positioned correctly in a scroll view while keeping the controls  at the place where they are in IB ?

**Answer:**
The Quartz coordinate system used in Mac OS X has the origin in the  
lower left corner just like Postscript, PDF, and every math textbook  
that contains a graph.

Western languages and historical CRT electron beams start in the  
upper left corner of the display and scan left to right down the  
display.

Placing an arbitrary view so that its top left corner corresponds to  
the top left cornet of a clip view (content view of a scroll view)  
that contains it is similar to the problem of centering an arbitrary  
view in a clip view.

I usually set the document view to be a flipped General/NSView instance and  
then add my custom (not flipped) view as a sub-view of the document  
view.  The document view can also be used to center the custom view,  
provide margins, etc.

Here is another solution that actually replaces the clip view:
http://www.bergdesign.com/missing_cocoa_docs/nsclipview.html

Here is a random sampling of the previous times this question was asked:
http://www.cocoabuilder.com/archive/message/2003/8/1/92472
http://www.cocoabuilder.com/archive/message/2003/10/30/1527
http://www.cocoabuilder.com/archive/message/2005/6/27/140185

I just whipped up a little example of exactly what the OP wants:
Custom view full of controls created in IB and programatically added  
to a scroll view so that the custom view stays in the top left corner  
of the scroll view.  Just for fun, I also include a mode to center  
the custom view in the scroll view.

Is there anyone willing to host this example so that it can be linked her ?
