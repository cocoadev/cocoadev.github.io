---
layout: page
title: MovingItemsWhenResizingWindow
---

How does one move items (say buttons) along with the window when the window is resized?  My app looks a lot like iTunes, and so consider that as an example.  When I drag the bottom right corner the main song list area resizes appropriately. (I did this by turning on Autosizing in the horiz and vert directions in Interface Builder.)  But buttons are not the same.  They should move with the resizing action, but not resize themselves.  More precisely, the effect I want to get is to lock the right edge of a button to a fixed distance away from the right edge of the window.  Thanks!

----

The four lines **outside** the box on the top/left/bottom/right can be springified too.  To get a button to stay a fixed distance from the right, make the outside left line 'springy' and the other 5 lines all 'straight'.  -- Bo

----

This is from Apple's IB FAQ docs:



* How do I set up the springs and struts so my views autosize?

Views can be made to resize automatically when the window or split view that contains them is resized. You will probably need to set the springs and struts for every view in your window. The easiest way to see how they work is to assign a few views with different settings and then use File > Test Interface to see the results.  To see how this works, bring up the Size info panel and select a square bevel button. There are two parts to Cocoa�s auto sizing: growing and anchoring. How the button grows is determined by the strut/spring setting shown inside the button picture in the info panel. If an axis (horizontal or vertical) is a spring, then the view can grow when its container is resized. If it�s not a spring, but rather a strut, the button will remain the same size.  Click on the horizontal axis inside the mock button to turn it into a curly spring. Then use the menu item File > Test Interface to test the interface. Grow the window in various directions. The button gets longer, but never taller or shorter. Click the switch icon in the menu bar or press Command-Q to stop testing the interface. Repeat this process, but with the vertical axis set as a spring. Now it can get taller or shorter but never wider. The outer set of springs and struts represents how the view is to be anchored. If a segment is a strut, the view maintains a constant distance to the side of its container along that side. However, if it�s a spring the view allows the distance to vary. Select the sample square bevel button and click the left-most segment so that it turns into a spring. Because the segment at right is still a strut, the view maintains a constant distance to the edge of the window from the view�s right side, but allows a variable distance along the left side. Test the interface to see the effect.


Also, I suggest building and running the Sproing example [http://developer.apple.com/samplecode/Sproing/Sproing.html]. That will allow you to play with the springs and see the results in real time. Pretty fun.

----

Thanks folks!

----
Does autosizing not work with rotated views?

