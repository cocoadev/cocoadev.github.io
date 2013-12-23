---
layout: page
title: PantherToTiger
---

This page is for recording "gotchas" that you have experienced in moving your application from Panther to Tiger. These may be problems that surfaced due to an undocumented (or poorly documented) change to an API. Or just problems due to bugs in your code that didn't have any significant effect until Tiger. This is *not* the place for discussion of new APIs in Tiger.



*
If you pass zero for the 'rowBytes' argument of NSBitmapImageRep<nowiki/>s initWithBitmapDataPlanes, then you can't assume that the data will be contiguous - there might be padding bytes added to the end of each row. This is new with Tiger - it is due to an optimization for drawing speed. In my experiments, it seems that each row is padded out to be a multiple of 32 bytes.

The documentation says:
*
The rowBytes argument is the number of bytes that are allocated for each scan line in each plane of data. A scan line is a single row of pixels spanning the width of the image.
Normally, rowBytes can be figured from the width of the image, the number of bits per pixel in each sample (bps), and, if the data is in a meshed configuration, the number of samples per pixel (spp). However, if the data for each row is aligned on word or other boundaries, it may have been necessary to allocate more memory for each row than there is data to fill it. rowBytes lets the object know whether that�s the case. If rowBytes is 0, the NSBitmapImageRep assumes there�s no empty space at the end of a row.
*

That was correct for Panther, but in Tiger, the semantics have changed and the documentation (as of May 1, 2005) has not caught up. In Tiger, when you pass zero for 'rowBytes', it means that the layout of the data in memory will be determined by the library and that you need to use the 'bytesPerRow' accessor to query the NSBitmapImageRep to find out the actual value used.

*
Under Panther a drop-able CustomView could be made invisible/hidden and continue to work. This behaviour does not occur under Tiger. 

*
Putting an NSAttributedString with an NSFileWrapper (as NSTextAttachment) into the menu of an NSPopupButton causes a crash when the user opens the menu (worked on Panther).

