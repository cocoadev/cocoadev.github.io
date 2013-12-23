---
layout: page
title: CoreImageVsCoreGraphics
---

Hey

Not a 100% cocoa question, but I have been trying to make gradients appear on a NSView background for some time now. I am trying to also do it as quickly as possible with minimal effort from the CPU. I have stumbled upon numerous examples and solutions and have read up on all of them. One method, the method used by a library called CTGradient utilizes Core Graphics (CG methods). Another method uses Core Image. I was wondering, whats the difference between Core Image and Core Graphics. I cannot find anything comparing them. Is Core Image simply a higher abstraction of Core Graphics, but designed for images?

Thanks

----
Think of CoreImage as a high-level abstraction around OpenGL for 2D pixel operations. It uses the GPU for hardware acceleration whenever possible. CoreGraphics currently only runs on the CPU.
----

CoreGraphics is basically a drawing library. Core Image is a bitmap image processing library. And although CoreImage is GPU-accelerated, CoreGraphics tends to be faster � really fast, actually � in my experience.

----
CoreImage will be vastly faster doing operations it's good at with data that stays on the video card. But CoreImage is so specialized that it makes little sense to compare them.

