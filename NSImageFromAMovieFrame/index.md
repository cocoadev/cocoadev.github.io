---
layout: page
title: NSImageFromAMovieFrame
---



Here is better code for extracting an NSImage from a GWorld that doesn't involve copying pixel by pixel and is easily generalized - ChrisMeyer

    

-(NSImage *)imageFromGWorld:(GWorldPtr)gworld
{
    NSParameterAssert( gworld != NULL );
    
    PixMapHandle pixMapHandle = GetGWorldPixMap( gworld );
    if ( LockPixels( pixMapHandle ) )
    {
        Rect portRect;
        GetPortBounds( gworld, &portRect );
        int pixels_wide = (portRect.right - portRect.left);
        int pixels_high = (portRect.bottom - portRect.top);
        
        int bps = 8;
        int spp = 4;
        BOOL has_alpha = YES;
        
        NSBitmapImageRep *bitmap_rep = [[[NSBitmapImageRep alloc]
            initWithBitmapDataPlanes:NULL
                          pixelsWide:pixels_wide
                          pixelsHigh:pixels_high
                       bitsPerSample:bps
                     samplesPerPixel:spp
                            hasAlpha:has_alpha
                            isPlanar:NO
                      colorSpaceName:NSDeviceRGBColorSpace
                         bytesPerRow:NULL
                        bitsPerPixel:NULL] autorelease];
        
        CGColorSpaceRef dst_colorspaceref = CGColorSpaceCreateDeviceRGB();
        
        CGImageAlphaInfo dst_alphainfo = has_alpha ? kCGImageAlphaPremultipliedLast : kCGImageAlphaNone;
        
        CGContextRef dst_contextref = CGBitmapContextCreate( [bitmap_rep bitmapData],
                                                             pixels_wide,
                                                             pixels_high,
                                                             bps,
                                                             [bitmap_rep bytesPerRow],
                                                             dst_colorspaceref,
                                                             dst_alphainfo );

        void *pixBaseAddr = GetPixBaseAddr(pixMapHandle);
        
        long pixmapRowBytes = GetPixRowBytes(pixMapHandle);
            
        CGDataProviderRef dataproviderref = CGDataProviderCreateWithData( NULL, pixBaseAddr, pixmapRowBytes * pixels_high, NULL );

        int src_bps = 8;
        int src_spp = 4;
        BOOL src_has_alpha = YES;
        
        CGColorSpaceRef src_colorspaceref = CGColorSpaceCreateDeviceRGB();
        
        CGImageAlphaInfo src_alphainfo = src_has_alpha ? kCGImageAlphaPremultipliedFirst : kCGImageAlphaNone;
        
        CGImageRef src_imageref = CGImageCreate( pixels_wide,
                                                 pixels_high,
                                                 src_bps,
                                                 src_bps * src_spp,
                                                 pixmapRowBytes,
                                                 src_colorspaceref,
                                                 src_alphainfo,
                                                 dataproviderref,
                                                 NULL,
                                                 NO, // shouldInterpolate
                                                 kCGRenderingIntentDefault );
        
        CGRect rect = CGRectMake( 0, 0, pixels_wide, pixels_high );
        
        CGContextDrawImage( dst_contextref, rect, src_imageref );
        
        CGImageRelease( src_imageref );
        CGColorSpaceRelease( src_colorspaceref );
        CGDataProviderRelease( dataproviderref );
        CGContextRelease( dst_contextref );
        CGColorSpaceRelease( dst_colorspaceref );
        
        UnlockPixels( pixMapHandle );
        
        NSImage *image = [[[NSImage alloc] initWithSize:NSMakeSize(pixels_wide, pixels_high)] autorelease];
        [image addRepresentation:bitmap_rep];
        return image;
    }
    return NULL;
}



----

This is some code I worked up a while ago to extract a single frame from a QuickTime movie and return it as an NSImage. Portions of this code are derived from Apple's QuickTime sample code, specifically CocoaVideoFrameFromNSImage ... which achieves almost what this code does, but not in such a self-contained manner. Apple's sample uses NSQuickdrawView, and I wanted something that was a bit more abstract and not tied to an NSView class. -- DrewThaler

    
// ---------------------------------------
//  imageFromMovie:atTime:
// ---------------------------------------
//
- (NSImage*)imageFromMovie:(Movie)movie atTime:(TimeValue)time
{
    // Pin the time to legal values.
    TimeValue duration = GetMovieDuration(movie);
    if (time > duration)
        time = duration;
    if (time < 0) time = 0;
    
    // Create an offscreen GWorld for the movie to draw into.
    GWorldPtr gworld = [self gworldForMovie:movie];
    
    // Set the GWorld for the movie.
    GDHandle oldDevice;
    CGrafPtr oldPort;
    GetMovieGWorld(movie,&oldPort,&oldDevice);
    SetMovieGWorld(movie,gworld,GetGWorldDevice(gworld));
    
    // Advance the movie to the appropriate time value.
    SetMovieTimeValue(movie,time);
    
    // Draw the movie.
    UpdateMovie(movie);
    MoviesTask(movie,0);
    
    // Create an NSImage from the GWorld.
    NSImage *image = [self imageFromGWorld:gworld];
    
    // Restore the previous GWorld, then dispose the one we allocated.
    SetMovieGWorld(movie,oldPort,oldDevice);
    DisposeGWorld(gworld);
    
    return image;
}



// ---------------------------------------
// gworldForMovie:
// ---------------------------------------
//  Get the bounding rectangle of the Movie the create a 32-bit GWorld
//  with those dimensions.
//  This GWorld will be used for rendering Movie frames into.

-(GWorldPtr) gworldForMovie:(Movie)movie
{
    Rect        srcRect;
    GWorldPtr   newGWorld = NULL;
    CGrafPtr    savedPort;
    GDHandle    savedDevice;
    
    OSErr err = noErr;
    GetGWorld(&savedPort, &savedDevice);

    GetMovieBox(movie,&srcRect);
    
    err = NewGWorld(&newGWorld,
                    k32ARGBPixelFormat,
                    &srcRect,
                    NULL,
                    NULL,
                    0);
    if (err == noErr)
    {
        if (LockPixels(GetGWorldPixMap(newGWorld)))
        {
            Rect        portRect;
            RGBColor    theBlackColor   = { 0, 0, 0 };
            RGBColor    theWhiteColor   = { 65535, 65535, 65535 };

            SetGWorld(newGWorld, NULL);
            GetPortBounds(newGWorld, &portRect);
            RGBBackColor(&theBlackColor);
            RGBForeColor(&theWhiteColor);
            EraseRect(&portRect);
            
            UnlockPixels(GetGWorldPixMap(newGWorld));
        }
    }
    
    SetGWorld(savedPort, savedDevice);
    NSAssert(newGWorld != NULL, @"NULL gworld");
    return newGWorld;
}


// ---------------------------------------
// imageFromGWorld:
// ---------------------------------------
// Convert contents of a gworld to an NSImage 
//
-(NSImage *)imageFromGWorld:(GWorldPtr) gWorldPtr
{
    PixMapHandle        pixMapHandle = NULL;
    Ptr                 pixBaseAddr = nil;
    NSBitmapImageRep    *imageRep = nil;
    NSImage             *image = nil;
    
    NSAssert(gWorldPtr != nil, @"nil gWorldPtr");

    // Lock the pixels
    pixMapHandle = GetGWorldPixMap(gWorldPtr);
    if (pixMapHandle)
    {
        Rect        portRect;
        unsigned    portWidth, portHeight;
        int         bitsPerSample, samplesPerPixel;
        BOOL        hasAlpha, isPlanar;
        int         destRowBytes;

        NSAssert(LockPixels(pixMapHandle) != false, @"LockPixels returns false");
    
        GetPortBounds(gWorldPtr, &portRect);
        portWidth = (portRect.right - portRect.left);
        portHeight = (portRect.bottom - portRect.top);
    
        bitsPerSample   = 8;
        samplesPerPixel = 4;
        hasAlpha        = YES;
        isPlanar        = NO;
        destRowBytes    = portWidth * samplesPerPixel;
        imageRep        = [[NSBitmapImageRep alloc] initWithBitmapDataPlanes:NULL 
                                                                pixelsWide:portWidth 
                                                                pixelsHigh:portHeight 
                                                            bitsPerSample:bitsPerSample 
                                                        samplesPerPixel:samplesPerPixel 
                                                                hasAlpha:hasAlpha 
                                                                isPlanar:NO
                                                          colorSpaceName:NSDeviceRGBColorSpace 
                                                             bytesPerRow:destRowBytes 
                                                            bitsPerPixel:0];
        if (imageRep)
        {
            char    *theData;
            int     pixmapRowBytes;
            int     rowByte,rowIndex;

            theData = [imageRep bitmapData];
        
            pixBaseAddr = GetPixBaseAddr(pixMapHandle);
            if (pixBaseAddr)
            {
                pixmapRowBytes = GetPixRowBytes(pixMapHandle);
            
                for (rowIndex=0; rowIndex< portHeight; rowIndex++)
                {
                    unsigned char *dst = theData + rowIndex * destRowBytes;
                    unsigned char *src = pixBaseAddr + rowIndex * pixmapRowBytes;
                    unsigned char a,r,g,b;
                    
                    for (rowByte = 0; rowByte < portWidth; rowByte++)
                    {
                        a = *src++;     // get source Alpha component
                        r = *src++;     // get source Red component
                        g = *src++;     // get source Green component
                        b = *src++;     // get source Blue component  
            
                        *dst++ = r;     // set dest. Alpha component
                        *dst++ = g;     // set dest. Red component
                        *dst++ = b;     // set dest. Green component
                        *dst++ = a;     // set dest. Blue component  
                    }
                }
            
                image = [[[NSImage alloc] initWithSize:NSMakeSize(portWidth, portHeight)] autorelease];
                if (image)
                {
                    [image addRepresentation:imageRep];
                    [imageRep release];
                }
            }
        }
    }

    NSAssert(pixMapHandle != NULL, @"null pixMapHandle");
    NSAssert(imageRep != nil, @"nil imageRep");
    NSAssert(pixBaseAddr != nil, @"nil pixBaseAddr");
    NSAssert(image != nil, @"nil image");

    if (pixMapHandle)
    {
        UnlockPixels(pixMapHandle);
    }

    return image;
}


Unless I'm missing something, this code doesn't seem to respect movies that have had changes made to Hue, Saturation, Brightness, Contrast, etc. ?

