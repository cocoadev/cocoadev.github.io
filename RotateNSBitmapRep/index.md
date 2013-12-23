---
layout: page
title: RotateNSBitmapRep
---

Hi,
I am applying border to a image. To do so I am using the bitmaprep (NSBitmapRep) of both image and merging them into one. For wider images i need to rotate border image by 90 degrees & then apply the border. Is there a way to rotate NSBitmapRep?

Thanks

----

*Create a new NSBitmapImageRep with the appropriate dimensions
*Create an NSGraphicsContext using your new image rep
*Make this graphics context current
*Apply an appropriate affine transform
*Draw your old image into the new one
*Your new image rep now contains the old one, rotated

