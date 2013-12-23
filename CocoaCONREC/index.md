---
layout: page
title: CocoaCONREC
---

This is pretty esoteric, but who knows, someone out there might get some use out of it. CONREC is an algorithm for generating contour lines from 3D data. Applications include mapping, weather maps, scientific visualization, etc, etc. The original paper is described here: http://local.wasp.uwa.edu.au/%7Epbourke/papers/conrec/

I've ported it to Cocoa and made it a bit easier to use within a Cocoa application context. I also did some work on optimising CONREC somewhat, and doing something about converting its output to an ordered vector form (i.e. bezier paths rather than randomly output line segments). Hopefully it will be of use to somebody, somewhere, somewhen. Demo application with full source at http://apptree.net/conrec.htm

http://apptree.net/images/conrec.jpg

(Disclaimer: the demo code (ab)uses a view class to act partially as a controller since I was too lazy to strictly implement MVC. The plotter part is meant to be the more useful/interesting/correctly written class).

--GrahamCox

