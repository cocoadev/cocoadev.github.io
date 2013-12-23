---
layout: page
title: SpinningProgressIndicatorImage
---

Does anyone know where on my mac I can find the source image for the spinning progress indicator?
http://www.music-inspiration.com/progress.png

----

Well, I tell ya what, you could just take a screenshot with it up and going.

Jacob

----

Well, it'll probably be in HIToolbox.rsrc, but that's an implementation detail and may change in a future release of the operating system. What is your real goal? Depending on what you're trying to do, you might be able to draw it into a CGContext using HIThemeDrawChasingArrows. --FinlayDobbie

