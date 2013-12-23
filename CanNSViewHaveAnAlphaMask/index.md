---
layout: page
title: CanNSViewHaveAnAlphaMask
---

Just wondering whether I can have transparent areas in an NSView, allowing its parent view to show through it, or is this not possible? Would [NSColor clear] set an area to transparent in this manner?

- Peter

----

For a custom view? just return NO from      - (BOOL)isOpaque . No need to paint yourself with the clear color. Off the top of my head, NO may be the default. Just draw what you need to, the rest will be transparent.

----

However, I am thinking about an OpenGL View (sorry, I totally omitted this from the original question) - Is it possible to do something similar with an OpenGL View - to set clear areas of it. OpenGL View IS a subclass of NSView - so I suppose I could try overriding the  draw method to fill areas with clearColor to try to overide the fact that it looks like there must be a base background color for the OpenGL View.

Is this the case? Or is there a way I could try to get transparency for certain areas in an OpenGL View.

 - Peter

----

Does this help? -> http://www.boredastronaut.com/software/files/GLCubes.zip

