---
layout: page
title: WhoOwnsNSBitmapImageRepBuffers
---

I am not clear on when  to release data buffers in an General/NSBitmapImageRep. If I create the object 
from a file like this:

    imageRep = General/[NSBitmapImageRep imageRepWithContentsOfFile: imagePath ];

then clearly releasing the object:

    [imageRep release];

will also release the buffers. But how about if I create it this way:

    [imageRep initWithBitmapDataPlanes: buffers
                        pixelsWide: width
                        pixelsHigh: height
                        bitsPerSample: 8
                        samplesPerPixel: 4
                        hasAlpha: YES
                        isPlanar: YES
                        colorSpaceName: General/NSCalibratedRGBColorSpace
                        bytesPerRow: width
                        bitsPerPixel: 0	];

Here I am using buffers that I have allocated. Are they released when I release the imageRep,
or does it keep track of who owns the buffers and does not release them? Or something else?

----

The buffers you have created are not managed by the image rep. The only buffer that is owned by the image rep is the buffer referenced by the value returned with the call to -bitmapData

    
   id bitmapImageRep = General/[[NSBitmapImageRep alloc] initWithBitmapDataPlanes: buffers
                        pixelsWide: width
                        pixelsHigh: height
                        bitsPerSample: 8
                        samplesPerPixel: 4
                        hasAlpha: YES
                        isPlanar: YES
                        colorSpaceName: General/NSCalibratedRGBColorSpace
                        bytesPerRow: width
                        bitsPerPixel: 0];
   char stackBuffer[1024];
   char *heapBuffer = malloc(1024);
   char *bitmapData = [bitmapImageRep bitmapData];


"stackBuffer" and "heapBuffer" are separate from the data pointed to by *bitmapData. You don't have to manage stackBuffer but you do have to manage heapBuffer and bitmapImageRep. When you release bitmapImageRep the data that is pointed to by *bitmapData is also released, so you cannot access this data any more.

----

In your example, is bitmapData the same as "buffers" (or &buffers[0]" for planar data), or does initWithBitmapDataPlanes allocate new buffers, leaving the caller free to release "buffers"?

----

There isn't a variable named buffers in my example, What is "buffers" is it a "stack" buffer, is it allocated from the heap or is it just a pointer?

----

Hmmm... yes there is. It's the first argument to General/[[NSBitmapImageRep alloc] initWithBitmapDataPlanes: For purposes of discussion, let's say it is allocated from the heap.

I think the answer is that all the buffers pointed to by "buffers" are owned by bitmapImageRep after this call, and will be released when bitmapImageRep is released. But I'd like to verify that.

----

Oh, that was a cut and paste oversight. I'm not sure either. I don't think you can retain data in C, but maybe you can. I'll sit this one out......

----

**The documentation clearly states the following...**

For the case above, substitute *planes* with *buffer*

*
If planes is NULL or an array of NULL pointers, this method allocates enough memory to hold the image described by the other arguments. You can then obtain pointers to this memory (with the getBitmapDataPlanes: or bitmapData method) and fill in the image data. In this case, the allocated memory will belong to the object and will be freed when it�s freed.

If planes is not NULL and the array contains at least one data pointer, the object will only reference the image data; it won�t copy it. The buffers won�t be freed when the object is freed.
*

Which means, if you allocated it, you own it. If not, don't worry about it.

Cheers,

- General/NeilVanNote
