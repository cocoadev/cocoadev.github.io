---
layout: page
title: UpdatingScrollers
---

I have an General/NSImageView inside an General/NSScrollView. When the image is bigger than the window it's in, I want the user to be able to scroll the image by simply dragging the mouse inside it (like Preview does). To do this, I subclassed General/NSImageView and added a mouseDragged: method. There, I use General/NSClipView's scrollToPoint: method so scroll my view. And everything seems to be working fine, except that the scrollbars in the window do not move when I do this. Am I doing this the wrong way? Or do I just need to manually calculate where the scroller should be and use General/NSScroller's setFloatValue:knobPosition method?

Thanks in advance,
General/PabloGomez

Nevermind, I had not seen the reflectScrolledClipView: method.
