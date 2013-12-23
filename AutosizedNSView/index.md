---
layout: page
title: AutosizedNSView
---

Hi,

I am making an App which has a main view (NSView subclass) embedded in an NSScrollView subclass (no surprises, just basic additions). Within the main view are 1 to N subviews, where N is in the range 1-1000, but most often around 50. These subviews of the main view can be dragged about and positioned arbitrarily. Now my question is, how can I ensure that the main view is always large enough to hold all of it's subviews, but no larger? You see, my problem is mostly cosmetic (this also affects interface efficiency), as I dislike the NSScrollView's bars from being too big, or too small. I was listening for NSViewFrameDidChangeNotification on the subviews, and stopping them at the main view's border (reset their frame to within main views frame). Now I have changed that code, to instead grow and shrink the main view's frame, but some problems persist. If I move the topmost or rightmost subview it works as expected, but if I move a subview closest to the main view's X or Y axis, when the main view is resized, it is cropped or expanded at the top and right (as to be expected). Is there a way to take advantage of negative frame origins, or bounds transforms, which would save me from repositioning the subviews when cropping the main view from the bottom or left? I am pretty sure I need to handle the NSClipView too, as just fumbling with the view frame causes the scroll view to jump around wildly. Basically I am just fishing for someone elses experience in making such a view.

Thanks,
JeremyJurksztowicz

