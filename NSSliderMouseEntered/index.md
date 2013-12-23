---
layout: page
title: NSSliderMouseEntered
---



I need to be able to capture Mouse Entered and Mouse Exited events for an NSSlider.  I'm already subclassing the slider to figure out when the user starts and stops dragging by capturing the MouseDown event.  However, it looks like non of the other mouse events get sent.  Does anyone have some sample code that will do this?  Thanks!

----

You need to set up a tracking rect and make sure the window sends periodic updates in order to receive mouseEntered and mouseExited events. See addTrackingRect:. There's some good sample code both here and in the ADC if you need some help getting started.

