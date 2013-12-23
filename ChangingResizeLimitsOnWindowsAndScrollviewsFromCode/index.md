---
layout: page
title: ChangingResizeLimitsOnWindowsAndScrollviewsFromCode
---



I have a pretty simple window that has a custom view inside a scrollview, and a progress bar and "%complete" text field under it.

My program creates custom images which are displayed inside the custom view (sort of like the way Photoshop creates a new image of arbitrary size.)

I know how to size the image so that it displays inside the view properly.

I want to be able to adjust the resize limits of the view/scrollview and of the window so that the user can make the window larger as long as there's more of my image to display, but the window's maximum size prevents the user from making the window bigger once the view shows the entire image area. That's my ideal.

Changing the resize limits on the window requires that I allow for the room on the window taken up by the other elements on the window (currently my progress bar an NSTextField, but perhaps more gadgets in the future.) If I could create the window with a maximum size that properly shows my entire image, query the window for it's resize limits, and then adjust those limits up or down by the amount that my image changed in size. 

Failing that, I'd like to programmatically set the resize limits on my view (scrollview?) so that the scrolling box will resize up to the size of the image, and then stop growing. Right now my image pins to the lower left of my scrollview, which looks really stupid. If you resize the window such that the interior of the scrollview is larger than the image, larger, you get whitespace above and to the right of the image.


Duncan C

