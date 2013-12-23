---
layout: page
title: NSBitmapImageRep
---

NSImage<nowiki/>'s representation of a bitmap. These can be GIFs, JPEGs, TIFFs, PNGs, or raw bitmap data.

http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/Classes/NSBitmapImageRep_Class/Reference/Reference.html

----
NSBitmapImageRep also has the longest method name in the Cocoa frameworks:
    
initWithBitmapDataPlanes:
              pixelsWide:
              pixelsHigh:
           bitsPerSample:
         samplesPerPixel:
                hasAlpha:
                isPlanar:
          colorSpaceName:
            bitmapFormat:
             bytesPerRow:
            bitsPerPixel:

initWithBitmapDataPlanes:pixelsWide:pixelsHigh:bitsPerSample:samplesPerPixel:hasAlpha:isPlanar:colorSpaceName:bitmapFormat:bytesPerRow:bitsPerPixel:
----

Q: How do you to draw into a NSBitmapImageRep with a specific pixel format?

----

    
NSBitmapImageRep *bitmap = [[NSBitmapImageRep alloc] initWithBitmapDataPlanes:NULL /* bitmap, allocate thyself! */ pixelsWide:width pixelsHigh:height etc.]; // this specifies the pixel format
[NSGraphicsContext saveGraphicsState];
[NSGraphicsContext setCurrentContext:[NSGraphicsContext graphicsContextWithBitmapImageRep:bitmap]];
[NSGraphicsContext restoreGraphicsState];



You cannot use just any pixel format, though.  There are more formats supported for drawing _from_ than drawing _to_.  See the list at http://developer.apple.com/mac/library/documentation/GraphicsImaging/Conceptual/drawingwithquartz2d/dq_context/dq_context.html#//apple_ref/doc/uid/TP30001066-CH203-BCIBHHBB .

----

Huh.  I would have thought that I could use -[NSImage lockFocus];

----

Here's what lock focus does in detail.  After we can talk about what it means:


* Allocates a new buffer of memory. 
* Makes a graphics context with that memory.
* Does     [NSGraphicsContext saveGraphicsState]; [NSGraphicsContext setCurrentContext:newContext];
* Draws the image in the buffer.
* Returns control to the caller.


And -unlockFocus does this:


* Grabs the current context's memory and makes into a new NSImageRep (well, a private subclass thereof). 
*     [NSGraphicsContext restoreGraphicsState];
* Replaces the reps of the image with the new rep.
* Returns control to the caller.


Here are some conclusions:  

* -[NSImage lockFocus] conceptually draws into the image, not any of its reps. It leaves the original reps unmodified, but removes them from the image.
* It's lossy.  Prior to lockFocus, an NSImage might be backed by a PDF, or by bitmaps at 16x16, 32x32, and 512x512.  Lock focus will end up replacing those reps with one rep that has the new drawing.  
* It's not super cheap - it allocates memory and draws.  It's not super amazingly expensive, but you don't want to lockFocus willy nilly.


----

Cool beans.  So how about if I want to get an NSBitmapImageRep from an NSImage?

----

This way is performant ( and ONLY AVAILABLE in 10.6 and above! ):     [[NSBitmapImageRep alloc] initWithCGImage:[image CGImageForProposedRect:context:hints:]].  It will not allocate a new image buffer unless the image was PDF backed or something like that that is incompatible with a bitmap.  

CGImageForProposedRect is described at http://developer.apple.com/library/mac/#documentation/Cocoa/Reference/ApplicationKit/Classes/NSImage_Class/Reference/Reference.html.  This was originally written as 'CGImageWithRect', which either doesn't exist or isn't described in the NSImage reference yet, so I assume CGImageForProposedRect is correct.

The (rect, context, hints) business is explained at length in the 10.6 AppKit release notes.  It's necessary because NSImage can potentially contain more data than is expressible in a single NSBitmapImageRep, so you're saying how you want it done.  It's kind of like taking a 2 dimensional snapshot of a 3 dimensional object - what angle do you want?

----

Eh. I think I'll use     [[NSBitmapImageRep alloc] initWithData:[image TIFFRepresentation]].

----

I'll cut you.  This is very wasteful.  

Let's just look at allocations to get an idea.  Suppose your image consists of a 16x16 bitmap, a 32x32 bitmap, and a 512x512 bitmap.  Then TIFFRepresentation allocates a data with enough space to hold data for all of those bitmaps.  Then you turn around and init a bitmap from the data.  This allocates another buffer and decodes the TIFF to it.  In a best case scenario, two allocations the size of your original image compared to zero with the previous technique.  

Oh, and which of the three original bitmaps did you get as the new one anyway?  Good question!

It's worse than just the above, though.  In general, the secret to image performance on Mac OS X is to use identical image objects for identical drawing.  Every time a button with a house on it redraws, the house should be drawn with the same image object.  Otherwise you thrash caches up and down the graphics stack.  For example, when OpenGL is on, CG uploads image data to the graphics card before drawing it.  If you're discarding your images and making new ones from data, the data needs to be uploaded to the card again every time.  

TIFFRepresentation is a rather overly attractive method.  A correct use of TIFFRepresentation is one that sends the resulting data outside of the process.  For example, write to disk, write to pasteboard, write to network.  If you aren't sending the data out of the process, you probably shouldn't be using TIFFRepresentation.  

Another misconception:  TIFFRepresentation isn't lossless.  What if the original image was backed by a PDF?  You cannot losslessly encode that as a TIFF.  It's 'idempotent' or whatever, though.  Once you have TIFF data, TIFF -> image -> TIFF -> image -> TIFF etc. is all lossless.  It's just the first encode as TIFF that may be lossy.

----

Sorry! Sorry!  I've seen this done to make a bitmap too, what's the deal here?

    [image lockFocus];
    NSBitmapImageRep *bitmap = [[NSBitmapImageRep alloc] initWithFocusedViewRect:(NSRect){ {0, 0}, image.size} ];
    [image unlockFocus];

----

Welp, several issues.  One, this modifies the original image.  See discussion of lockFocus above.  Two, it allocates one new bitmap buffer for the lockFocus, then another for the initWithFocusedViewRect.  It also suffers from the cache-thrashing issues discussed above.  

----

How about if a trawl through [image representations] looking for a bitmap?

----

Go for it, but it's sort of a pain.  Note that you may not find one, so you need to fallback to the CGImage thing given above.  

Memory wise, if you find one, it's a constant factor better than using the CGImage thing.  You skip the allocation of the shell NSBitmapImageRep that wraps the CGImage.

----

One can use something like the following (the code below is not industrial strength, i.e. no error checking for type of incoming bitmap) to convert the bitmap to an offscreen g-world (or a PixMap) suitable for use in QuickTime, etc.

    
void CopyNSBitmapImageRepToGWorld(NSBitmapImageRep *bitmap, 
                                  GWorldPtr gWorldPtr)
{
    PixMapHandle pixMapHandle;
    Ptr pixBaseAddr, bitMapDataPtr;

    // Lock the pixels
    pixMapHandle = GetGWorldPixMap(gWorldPtr);
    LockPixels (pixMapHandle);
    pixBaseAddr = GetPixBaseAddr(pixMapHandle);

    bitMapDataPtr = [bitmap bitmapData];

    if ((bitMapDataPtr != nil) && (pixBaseAddr != nil))
    {
        int i;
        int pixmapRowBytes = GetPixRowBytes(pixMapHandle);
        NSSize imageSize = [bitmap size];
        for (i=0; i< imageSize.height; i++)
        {
            int j;
            unsigned char *src = bitMapDataPtr + i * [bitmap bytesPerRow];
            unsigned char *dst = pixBaseAddr + i * pixmapRowBytes;
            for ( j=0; j<imageSize.width; ++j )
            {
                *dst++ = 0;
                *dst++ = *src++;
                *dst++ = *src++;
                *dst++ = *src++;
            }
        }
    }

    UnlockPixels(pixMapHandle);
}


-ChrisMeyer

(The idea for this code was inspired by http://www.cocoadevcentral.com/tutorials/showpage.php?show=00000027.php ).

----

Another trick I found was to use a GraphicsImporterComponent to directly use the TIFF image instead of messing around with a G-world at all. The code below probably doesn't compile as-is but gives you some idea of how it works. As always, error checking should be added, etc.

    
NSData *tiff_data = m_frame [[TIFFRepresentation] retain];

MovieImportComponent tiff_import_component 
	= OpenDefaultComponent( GraphicsImporterComponentType, kQTFileTypeTIFF );
PointerDataRef data_reference 
       = (PointerDataRef)NewHandle( sizeof(PointerDataRefRecord) );

(**data_reference).data = (void *) [tiff_data bytes];
(**data_reference).dataLength = [tiff_data length];

GraphicsImportSetDataReference( tiff_import_component,
                                (Handle)data_reference,
                                PointerDataHandlerSubType );

GraphicsImportGetImageDescription( tiff_import_component, &image_description_handle );

CloseComponent( tiff_import_component );

// do something with the image description and tiff data
//

DisposeHandle( image_description_handle );
[tiff_data release];


Probably only useful for a subset of QT operations; but hopefully this will help someone out. -ChrisMeyer

----

Here is a piece of code that puts a bitmap image to the pasteboard.  I use it in a tool as a quick way to get a GWorld out of a program.  I can open Preview then do "New From Pasteboard" (The source is actually a Macintosh 32 bit GWorld for this code)  Does anyone have code that saves a NSImage as a file?

     
{
	NSBitmapImageRep *bmrep = [NSBitmapImageRep alloc];
	NSPasteboard *pb = [NSPasteboard generalPasteboard];

	bmrep = [bmrep initWithBitmapDataPlanes:&image->buffer_ptr
			pixelsWide:(GLint) image->buffer_size.width
			pixelsHigh:(GLint) image->buffer_size.height
			bitsPerSample:8
			samplesPerPixel:4
			hasAlpha:YES
			isPlanar:NO
			colorSpaceName:NSCalibratedRGBColorSpace
			bytesPerRow:image->buffer_size.width*4
			bitsPerPixel:32];

	[pb declareTypes:[NSArray arrayWithObjects:NSTIFFPboardType, nil] owner:nil];
	[pb setData:[bmrep TIFFRepresentation] forType:NSTIFFPboardType];
}


----

Note that NSBitmapImageRep does not make a copy of the bitmap planes you give it, it uses them in-place, so make sure not to free them. Keep them around in an NSData or something if you need to.

----

does someone ever used NSBitmapImageRep  colorizeByMappingGray, i  think it would be useful for changing greytint to pseudocolor images
but I can't get it to work , PaulCD

----

I have written IFF file format reader/writer class. Also supporting classes for file byte reader/writer and for generic bitmap object. These classes work together well. Now, I want to implement NSBitmapImageRep instead of my custom bitmap class. The main reason is so I can display the image on screen using NSImage and NSImageRep (and their sub classes). I try to find the bitmapData description (for raw data) but come up with nothing. Anyone have any idea? Do I have to translate my IFF data to TIFF data and use it that way? - ArtayaB

----



**I am trying to generate a 16 bit per channel offscreen Bitmap, to which I can draw to and save as a 16 bit Tiff File, but all I get is a black image.**

    
	NSBitmapImageRep* offscreen = [[NSBitmapImageRep alloc] initWithBitmapDataPlanes:NULL
							pixelsWide:396 
							pixelsHigh:396 
							bitsPerSample:16
							samplesPerPixel:4 
							hasAlpha:YES
							isPlanar:NO 
							colorSpaceName:NSDeviceRGBColorSpace
							bytesPerRow:0
							bitsPerPixel:0];
	 
	[NSGraphicsContext saveGraphicsState];
	[NSGraphicsContext setCurrentContext:[NSGraphicsContext 
							graphicsContextWithBitmapImageRep:offscreen]];
	 
	[[NSColor colorWithCalibratedRed:1 green:0 blue:0 alpha:1] set];
	[[NSBezierPath bezierPathWithRect:NSMakeRect(0, 0, 396, 396)] fill];

	[NSGraphicsContext restoreGraphicsState];

	NSData* TIFFData = [offscreen TIFFRepresentation];
	[TIFFData writeToFile:@"/Users/utsi/Desktop/temp.tiff" atomically:YES];


The same code works fine with "bitsPerSample:8". Any ideas ? /Utsi.

----

Instead of using the long initialiser of NSBitmapImageRep, you can also subclass NSImageRep. It's not difficult to implement and provides more flexibility.

