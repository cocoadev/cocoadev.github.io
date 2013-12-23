---
layout: page
title: ViewsOnTopOfQCView
---



I have an application that makes heavy use of a General/QCView that loads a custom Quartz Composition. I want to overlay that General/QCView with other views (like a progress bar, etc), but no view I attempt to place "on top" is ever seen.

Any ideas on how this is accomplished? 

-- Trent

you use a separate, transparent window above the one that contains the General/QCView...it's the same story with General/QTMovies...they simply redraw much more often than static widgets...

--General/EcumeDesJours

----
Overlapping sibling views are not supported by Cocoa.  A Subview will draw over its super-view, but it probably will not work correctly with General/NSOpenGLViews, General/QCViews, or General/QTViews because in each case, the content of those views is backed by memory on your graphics card, and the normal Cocoa view drawing algorithms are bypassed as an optimization.  The solution suggested above is the best bet.  Use a child window to over lap controls.  See Apple's sample: http://developer.apple.com/samplecode/General/GLChildWindowDemo/General/GLChildWindowDemo.html
