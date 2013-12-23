---
layout: page
title: ScreenSaverViewAndCoreAnimation
---

I'm playing around with Core Animation in a just-for-fun project, and I want to take advantage of it by doing a quick screen saver.

Trouble is that ScreenSaverView inherits from NSView, and its :drawRect draws a black screen. I am unsure how Core Animation layers interact with NSViews (the documentation on either subject do not shed much light), but I am under the impression that the default NSView implementation draws all layers attached (via NSView:setLayer).

The problem here is my inheritance tree, which I can't seem to get away from

NSView -> ScreenSaverView -> MyScreenSaverView

Since ScreenSaverView draws a black rect, that's all I can see. I am currently calling super:drawRect from MyScreenSaverView. Is there a way to access a superclass's superclass, so I can bypass ScreenSaverView's drawRect function entirely, and call the NSView drawRect?

Also, as a secondary question... How do subviews work under an NSView? Do their drawRects get called automatically when their parent view executes drawRect? I have attempted to sidestep this problem by attaching a view as a subview under my ScreenSaverView, and then attaching the Core Animation layers to *that*, but the subview does not draw.

