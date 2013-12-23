---
layout: page
title: ChangeNSViewBoundsIndependentlyOfScale
---

I am attempting to alter a NSView's frame and bounds as the view is scrolled to simulate it being clipped by another object which is not an NSView... so as my NSView slides into this other object, I shrink the frame and re-position the bounds.  But shrinking the frame causes the bounds to shrink to match the frame, then when I change the coordinate origin by the setBoundsOrigin call, the actual bounds width or height is too small.  So then I set the bounds width and height back to what I wanted in the first place, but this changes the transformation matrix in the view... Arrgh.  I try setting the scale back to 100% (or more specifically the same as the window), and now the bounds are changed again.

Is there any way to set the frame and bounds independently of each other without affecting the view's scale?

The documentation for NSView setFrame implies that if I set the bounds first, then the setFrame method will leave the bounds alone, but that doesn't seem to work for me, and maybe I'm reading it wrong.

----

Your question doesn't make sense, unfortunately..

For example, let's say your bounds and frame both have a width of 200. Now you set the frame to 100. By default, that will also set the bounds to 100. If the bounds stayed the same, as you wish, then you would have a frame width of 100 but a bounds width of 200. This means that you're scaling the view horizontally by 0.5.

You seem to think that there are three independent properties here, frame, bounds, and scale. In fact there are only two independent properties, frame and bounds. Scale is simply a consequence of the relationship between the frame and bounds. As such, changing all three independently doesn't make sense.

Why is it a problem that setting the frame sets the bounds?

----

It makes sense to me because I have the same problem. I want to have a view that always has a bounds of, say, 600 wide by 800 high, because that is the unit system that the contents work in. I'm going to put text in that view. Now I embed that view as a subview in another view. That superview can change size of its subview as the user changes the view scale, so the superview changes the frame of its subview. But, contrary to the NSVview docs, the bounds get recalculated instead of the scale. This is on 10.5.2. I may well have screwed up or read the docs wrong, but currently it is not working as I think it is advertised.

----
I'm pretty sure that NSView tries to hold a constant scale, not a fixed bounds size, even if you set your own bounds. The fix should be simple enough. Just listen for frame changed notifications on yourself (don't forget to enable them) and reset your bounds in there.

