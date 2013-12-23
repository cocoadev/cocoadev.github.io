---
layout: page
title: OutliningAnNSControl
---

I'm trying to develop a GUI builder. So far I can drag around views fine, but when I draw a bounds rectangle it's often much larger than the rect that InterfaceBuilder draws.

IB's rect is usually snug right up to the edge of the visible part of the control. My rects are a few pixels out from the visible part, but I think they do represent the true frameRect of the view. Im guessing the empty border is where the controls draw the highlites to indicate focus. 

I can't figure out how IB calculates it's rects though. The bounds rect and frame rect are exactly the same size, and there aren't any other methods I can find that return a rectangle.

----

The extra margin may also be used for shadows.

To be honest, I think there is a lot of hardcoded values in IB. Take a look at the AHIG, they list all the sizes for the various controls, I did not check to see if these were depicted with/without margin.

Can I ask you, why are you writing a new GUI builder? Will this be a full replacement for IB? If this project is "for real" (as *not* in, "I'm making this to get my hands around Cocoa, and it probably never will be done" ;) ) then I'd be very interested in your project.

--AllanOdgaard

----

The GUI builder is not going to be as full-featured as IB, but it will have some features that IB doesn't, like vector drawing right into the GUI. It's meant for a different type of user than IB, say HTML, or Flash developers. The app is half way between "real" and "not" I guess. I am just getting good at Cocoa, and this project is pushing me, but I do hope to release it one day.

I have thought that IB may be using hardcoded values for offsets from the frame rect, but man, I just don't like that solution. I also thought it might be creating a bounding rect around the NSCells in the control, which may be smaller than the frame, but I haven't tested that yet.

