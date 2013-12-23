---
layout: page
title: PrintingNSPDFImageRep
---

I had a problem that when I wanted to print a NSImage containing an NSPDFImageRep, a TIFF drawing of the image and its bounding box appeared, and the Arcs and Text of the image were bitmaps.

The problem was that the original PDF representation was being discarded, and a TIFF cached image was being retained.  

To fix this, you must tell the image to retain the original representation:

[image setDataRetained:YES];

I still haven't got rid of the bounding box and gray background, but at least the arcs and text are in full resolution!

This hint comes from:

http://www.omnigroup.com/mailman/archive/macosx-dev/2002-February/023366.html

