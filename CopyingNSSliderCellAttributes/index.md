---
layout: page
title: CopyingNSSliderCellAttributes
---

I want to create my own NSSlider and NSSliderCell. But when I add my own NSSliderCell to my NSSlider, the slider looks quite ugly (compared to the standard slider from InterfaceBuilder). OK, I could start to read out all layout attributes (like knob size, number of ticks, ...) from the original NSSliderCell but that is somehow inconvenient. Is there an easy way to copy attributes from a NSSliderCell (or a class in general)?

--ThomasSempf

----

NSCell conforms to NSCopying, have you tried just copying it directly? If that doesn't work, NSCell also conforms to NSCoding, so you could shove it through an archiver to get a copy.

