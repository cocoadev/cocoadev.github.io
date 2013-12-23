---
layout: page
title: WindowShadowOpacity
---

I have been racking my brains searching through every private General/CoreGraphics header I can find... but to no avail.  Does anyone know how to adjust the opacity/strength of a window's shadow?  Thanks in advance.

----Why do you need to do this?

---- Trust me when I say you don't want to know, but it's important.

---- 

You may wish to simply disable the shadow, then draw your own shadow using a child window.
Not a perfect solution but it will be compatible with future OS versions (insert standard warning about using private function calls here. Please don't).
