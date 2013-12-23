---
layout: page
title: CSourceImage
---

how do I make a NSImage from a C-Source image? (like the ones you can make with GIMP or GraphicConverter)

I have a struct like this:
    
static const struct {
  unsigned int 	 pd_width;
  unsigned int 	 pd_height;
  unsigned int 	 bytes_per_pixel; /* 3:RGB, 4:RGBA */
  unsigned char	 image_pixel_data[0xbf62];
}



--PtxMac

----

    
    NSBitmapImageRep *bir;
    BOOL hasAlpha;
    if (cSourceImage.bytes_per_pixel==3) hasAlpha=NO;
    else if (cSourceImage.bytes_per_pixel==4) hasAlpha=YES;

    int bitsPerSample=8;    //    < - usually 8

    bir=[[[NSBitmapImageRep alloc] initWithBitmapDataPlanes:nil
            pixelsWide:cSourceImage.pd_width
            pixelsHigh:cSourceImage.pd_height
            bitsPerSample:bitsPerSample   
            samplesPerPixel:cSourceImage.bytes_per_pixel	
            hasAlpha:hasAlpha
            isPlanar:YES
            colorSpaceName:NSDeviceRGBColorSpace
            bytesPerRow:cSourceImage.bytes_per_pixel*cSourceImage.pd_width
            bitsPerPixel:bitsPerSample*cSourceImage.bytes_per_pixel] autorelease]; 

    char *bytes=[bir bitmapData];
    int byteCount=cSourceImage.bytes_per_pixel*cSourceImage.pd_width*cSourceImage.pd_height;
    memcpy(bytes, cSoureImage.image_pixel_data, byteCount);

    NSImage *myImage;
    myImage=[[[NSImage alloc] initWithData:[bir TIFFRepresentation]] autorelease];

    


NSBitmapImageRep's documentation is pretty good. --zootbobbalu

