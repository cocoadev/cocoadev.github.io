---
layout: page
title: CoordinatingImageProcessingLibraries
---



I'm working on an app that does some batch image processing.  It looks like the functionality I need is provided in three different libraries - Quartz 2D, vImage, and Core Image.  All three of these use different data structures to handle image data, so I guess there's a lot of data conversion in my future, but I'm not sure how best to do it.  Core Image provides an easy way to convert between CGImage and CIImage, but I'm blanking out on a good way to pass data from either of these into vImage other than by using NSImageRep as an intermediary.  I expect there has to be something easier - any ideas?  --SeanUnderwood

