---
layout: page
title: SplitButtonView
---

Is there a nicer split button than NSSegmentedControl?

Like in iCal, it's:

http://www.wildoctagon.com/icalsplitview.jpg

instead of

http://developer.apple.com/documentation/Cocoa/Conceptual/SegmentedControl/Art/ical-alike.gif


It also has that in Safari and iPhoto and Finder and probably other stuff?

How do they do it?

* Custom images*



Is there a way to do it without custom images? Can I easily implement this in my own apps?

----

This button style is known as "plastic", and you can make your segmentview look like that two ways: Either make the window containing the segmentview brushed, or put the segmentview into a unified toolbar. Viola! Plastic-themed NSSegmentView. I've posted screenshots of this below:

http://iindigo3d.com/segmentaqua.png
http://iindigo3d.com/segmentbrushed.png

Hope this helps! --JohnWells

