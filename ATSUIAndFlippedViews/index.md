---
layout: page
title: ATSUIAndFlippedViews
---

Does anybody know the recommended way of drawing text (using ATSUI) in an NSView with flipped coordinates?

Originally I was just supplying a {1, -1} scale transformation, but it messes up synthetic italic, ascend/descend information (which then is zero for the line) and the underline position.

----

I can supply a quick fix: draw it into and NSImage then render that onto your view.  There are certainly better ways though I'm sure.

- FranciscoTolmasky

