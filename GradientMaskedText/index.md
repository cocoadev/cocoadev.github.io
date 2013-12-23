---
layout: page
title: GradientMaskedText
---

Say I have, for example, I'm drawing text in a transparent layer. How would one make the text fade out in a particular direction, assuming the fact that we know nothing about the background?

----
I strongly assume you'd have to render the text into an image and then simply make a composite together with an alpha gradient. CoreGraphics (Quartz2D) has image masks, which would also be perfectly suited for that purpose, but as this here is COCOAdev, you can also go for the NSImage compositeToPoint: methods using an operation like NSCompositeSourceIn (see documentation).

--MarCocoa

