---
layout: page
title: CALayerInNSScrollViewInNSSplitView
---

Did anybody try to set layer - CALayer or CATiledLayer of a view embeded in NSScrollView in a NSSplitView. I had tried and the result was absolute disaster. I can't figure out how to make it work.

Appkit Release Notes states:
"Note that when using layer-backed mode for an NSScrollView's document view, it's necessary for the enclosing NSScrollView, or at least its NSClipView ("content view"), to also be layer-backed in order for scrolling to function correctly.". Did that help?

