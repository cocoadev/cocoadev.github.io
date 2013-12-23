---
layout: page
title: QuickDraw
---



QuickDraw is deprecated as of Mac OS X 10.4.  See http://developer.apple.com/documentation/Carbon/Conceptual/QuickDrawToQuartz2D/index.html for tips on moving to Quartz (aka CoreGraphics).

QuickDraw is the name of the 2D graphics library created for the Macintosh in the early 1980s. It was the foundation for rendering shapes and text to screens and printers. Mac OS X primarily uses Quartz for 2D graphics, but the QuickDraw API remains available through Carbon. You can use it in your Cocoa applications with NSQuickDrawView.

QuickDraw was originally designed for rendering bitmap graphics on a display. If you read an older book, like InsideMacintosh: Imaging With QuickDraw, you'll see a lot of talk about pixels and points and lines. This is a contrast to Quartz, which adds a higher-level abstraction including bezier paths, masks, strokes, fills, and so on. These are more like the concepts used in Photoshop or Illustrator to produce professional content. 

QuickDraw is still good at its job, and certain things with bitmaps are still easiest to do through QuickDraw ... but now that computing power is cheaper, displays are getting larger/better, and high-quality printers are the norm, the Mac world is moving away from bitmap graphics and toward vector graphics that can be arbitrarily scaled and still look nice.

