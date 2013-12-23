---
layout: page
title: HowToDesignTheViewForGraphingApplication
---



I'm a beginner at OS X Cocoa development, and I would like to develop an application that is much like a 'graphing' application. It would have X and Y axes and the user would be able to plot points on this graph.

Also, I am looking to only develop this application for Leopard, using the OS X 10.5 SDK.

For the view of this application, I would like to develop something similar to the 'Preview' application, in that the 'graph' or document would be drawn within the window and the user will be able to zoom in/out, and move the document around within the window (just look at a PDF within Preview to see what I am thinking of).

I need some guidance as to how to implement this....a few issues I have been thinking about:

1. What is the easiest/most efficient way to implement zooming and repositioning of the document within the window frame?
2. How should my application model code interpret points spawned from the mouse events? To elaborate on this.....if the user clicks on the coordinate (-3,4) in my 'graph' of the document, how can I determine this is the case from the user space coordinates accessible from the mouse events? Moreover, how is this handled with zooming and repositioning involved....i.e. if the user clicks on the same point with the document zoomed out to 25%, they will not be clicking on the same point relative to the 'graph'. My thinking is that I will need a separate coordinate space that I will need to convert back and forth between....or is this unneeded?

----

Well, you've started to BreakItDown a little bit, so that's a good sign, but unfortunately it seems that you've attacked the high-level UI stuff first.  Why not spend more time designing your application before worrying about how hit testing will work?

----

Read up on NSView. None of the issues you are worrying about really are issues when you understand it. Cocoa views are effectively resolution independent - you have a base co-ordinate system that doesn't change and you can set zooming using methods in a view and all the scaling is taken care of for you. Co-ordinate conversion is automatic so as far as hit-testing is concerned your mouse hits the same point you can see (though with less precision if you are zoomed far out, for example). Just draw your graph into the same co-ordinate space and the zooming is a visual effect only. Regarding graph plotting and handling this zoom, these classes may help you: http://apptree.net/gczoomview.htm and http://apptree.net/parser.htm --GC

