---
layout: page
title: ImageBackgroundNSCell
---



How do i make an image background in a NSCell? Like the album list in iTunes.

*Seems like the easiest way is to subclass NSCell, then draw an NSImage (possibly using NSImageCell) whenever     drawInteriorWithFrame:inView: is called --JediKnil*

