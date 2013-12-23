---
layout: page
title: NSImageCacheMode
---



The     NSImageCacheMode EnumeratedType describes how an     NSImage caches its data. Note that all image caching is done by     NSImage, *not* by     NSImageRep.

You can get an image's cache mode with     -[NSImage cacheMode] and change it with     -[NSImage setCacheMode:].

Due to a bug in     NSPDFImageRep, you should always draw it without caching. There are a couple ways to do this:

* Change its containing image's cache mode to     NSImageCacheNever.
* Always draw it using     NSImageRep's     -draw� methods. Since caching is done by     NSImage,     NSImageRep ignores it.


You can also use     NSImageCacheAlways to force the image to be preloaded before drawing.

