---
layout: page
title: DataWithPDFInsideRect
---

If I try to create a dataWithPDFInsideRect representation of a view while printing, the applications bombs. It seems that using this method while printing is not legal. Is there anyway to access it from within a printing context?

Any help appreciated.

----

Why do you need to do this? When printing, you are creating a PDF from your view by definition, so there doesn't appear to be any legitmate reason to do this explicitly at this time. Sounds like your design is off. --GC

