---
layout: page
title: TrackingSlider
---

In my work, which is mainly around simulations, I have a number of mechanisms like pistons and servos which are controlled by sliders, but which ( since they're representations of "real" physical devices ) cannot immediately respond.

To give the user feedback, I came up with a subclass of General/NSSlider I call General/TrackingSlider. Here's a screenshot of it in action:

http://home.earthlink.net/~zakariya/files/General/TrackingSlider-Example.gif

The idea is that the little triangle below the slider gives feedback in real-time as to the current value of the mechanism being set by the slider. E.g., you drag the slider to the right, and the little black triangle slides to the right reflecting the speed at which the controlled obkect can respond.

There's too much code to post here. So I'm linking to a demo app with full source code. Here's a screenshot of the demo app:
http://home.earthlink.net/~zakariya/files/General/TrackingSliders.gif

And here's the source code ( General/XCode 1.1):
http://home.earthlink.net/~zakariya/files/General/TrackingSlider.zip

I hope somebody finds this useful.

--General/ShamylZakariya

----

Sweet!  Thanks a lot!
