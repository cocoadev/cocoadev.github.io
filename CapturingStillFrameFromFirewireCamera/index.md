---
layout: page
title: CapturingStillFrameFromFirewireCamera
---



Does anyone have code to grab a still frame from a FireWire cam and save it to a file?

All the examples I've found are for capturing 'live' video and displaying it in a view. For my purposes, I don't want to display it, and I only need a still frame, not a movie. Or is a still just treated as a single frame 'movie'?

I have the Cocoa SGDataProc sample, but am at a loss what to do with it.

QuickTime sux.

Thanks for any help.

-Paul

----

Take a look at
    http://www.mulle-kybernetik.com/software/MulleSight/

You may also be interested in
      http://ivideo.sourceforge.net/
      
-Jason

----
I've been using the libARVideo code from the ARToolkit: http://www.hitl.washington.edu/artoolkit/download.htm#Linux
(download version 2.61 and look at ARToolkit2.61/lib/SRC/VideoMacOSX/video.c) 

The code enables you to initialise the camera and take snapshots using a single function, returning the resulting image as an array of unsigned chars, which can be easily converted to an NSBitmapImageRep if required.

-Jon

