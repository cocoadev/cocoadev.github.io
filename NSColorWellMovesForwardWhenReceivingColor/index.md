---
layout: page
title: NSColorWellMovesForwardWhenReceivingColor
---

I'm working on a drawing application, and I have two NSColorWells that are used for foreground/background color.

The background color well is positioned under the foreground well and offset 50%.

Basically, when a color is dragged to the background color, it moves forward, in front of the foreground color.

I'm trying to figure out how to fix this behavior.

Here is a video showing the problem:
http://sipefree.com/media/color-wells.mov

Cheers.



----
You really shouldn't overlap components - the behavior is undefined... So maybe you should consider creating your own component rather than using colorwells.

