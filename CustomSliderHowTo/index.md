---
layout: page
title: CustomSliderHowTo
---

Could not find this using search, so forgive me if this already exists.

I would like to make a similar slider like QuickTime / iTunes has, How is this done?

----

I think you need to make your question more specific. To make a custom control, you can subclass NSControl and/or NSCell, or maybe just NSView. --GC

----
Sorry for the vagueness, I was more looking for the three split way of drawing controls. Having a left, middle and right side of the track line for instance.

----

As the previous poster said, you can override NSControl, or in your case NSSlider and NSSliderCell, and draw whatever you want. If you have three images for the left, middle, and center of a track, you can draw them all in the same control. --LoganCollins

