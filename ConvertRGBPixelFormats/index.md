---
layout: page
title: ConvertRGBPixelFormats
---

I'm reading some RGB565 data from a binary file, and am needing to convert it to RGB888 (supposedly NSBitmapImageRep doesn't handle RGB565) and found some code from the Cocoa mailing list that attempts to do this. Now in the beginning I didn't really know what I was doing with RGB data at all, so I had to read up on it and now I have a fairly well understanding of the differences of RGB 555, 565, and 888 data. This function below gives me images that looks really screwy from what they should be, although I can tell that most of the colors are there in the image (just not in the right pixel location). Am I correct in saying that with RGB565/888 pixels are laid out from left to right, top to bottom? Anyways, if someone could give me a few pointers for reading RGB565 data, or why this method is not working properly, or anything I would really appreciate it. BTW, I've attempted to just hard code my own RGB888 into the code (like 0xFF0000FF for red) and that works fine. I think there's just something wrong with how its reading the data (or maybe my RGB565 data is screwy??). Thanks.

    // swizzle pixels from RGB565 to RGBA8888
#define SRC_PIXEL    UInt16
#define DST_PIXEL    UInt32
#define RED(x)        (((x) << 16) & 0xF8000000)
#define GREEN(x)    (((x) << 13) & 0x00FC0000)
#define BLUE(x)        (((x) << 11) & 0x0000F800)
#define ALPHA(x)    0x000000FF

NSImage *ConvertRGB565ToNSImage(unsigned char *data, int width, int height)
{
    register SRC_PIXEL *src;
    register DST_PIXEL *dest;
    NSImage* image;
    NSBitmapImageRep *bitmap;
    int row, col, dstRowBytes;
	
    bitmap = [[NSBitmapImageRep alloc]
            initWithBitmapDataPlanes: nil
                          pixelsWide: width
						  pixelsHigh: height
					   bitsPerSample: 8
					 samplesPerPixel: 4
                            hasAlpha: YES
							isPlanar: NO
                      colorSpaceName: NSCalibratedRGBColorSpace
						 bytesPerRow: 0
						bitsPerPixel: 32];
	
    src = (SRC_PIXEL *) (data);// + sizeof(unsigned char));
    dest = (DST_PIXEL *) [bitmap bitmapData];
    dstRowBytes = [bitmap bytesPerRow];
	//int n = 4; //[srcImageRep bitsPerPixel] / 8;
	
	int count = 0, x, y;
	for (y=0; y<height; y++)
    //for (row = height; row > 0; row--)
    {
		for (x=0; x<width; x++)
        //for (col = width; col > 0; col--)
        {
            DST_PIXEL pixel = *src++;

			//unsigned long pixel = (*src) * (y * width + (width/2));
            *dest++ = RED(pixel) | GREEN(pixel) | BLUE(pixel) | ALPHA(pixel);
			
			count++;
        }
        
        // i have no idea what this below does...
        //dest = (DST_PIXEL*) ((unsigned char*)dest + dstRowBytes - width*sizeof(DST_PIXEL));
    }
	
    image = [[[NSImage alloc] initWithSize:NSMakeSize(width, height)] autorelease];
    [image addRepresentation:bitmap];
    [bitmap release];
	
    return image;
}

----

OK after spending weeks on and off trying to fix this (sort of a side project), it now works. Here's the code which reads RGB565 data and creates an NSImage out of it:
    
unsigned long RGB565toRGBA(unsigned short rgb565)
{
	unsigned short temp = CFSwapInt16LittleToHost(rgb565); // swap bytes. may not be needed depending on your RGB565 data
	unsigned char red, green, blue;
	red = (temp & 0xF800) >> 11;
	green = (temp & 0x07E0) >> 5;
	blue = (temp & 0x001F);	
	red *= 8;
	green *= 4;
	blue *= 8;
	return (red << 24) | (green << 16) | (blue << 8) | 0xFF;
}

NSImage *ConvertRGB565ToNSImage(unsigned char *data, int width, int height)
{
	unsigned short *src;
	unsigned long *dest;
	NSImage* image;
	NSBitmapImageRep *bitmap;
	int dstRowBytes;
	
	bitmap = [[NSBitmapImageRep alloc]
            initWithBitmapDataPlanes: nil
                          pixelsWide: width
						  pixelsHigh: height
					   bitsPerSample: 8
					 samplesPerPixel: 4
                            hasAlpha: YES
							isPlanar: NO
                      colorSpaceName: NSCalibratedRGBColorSpace
						 bytesPerRow: width*4
						bitsPerPixel: 32];
	
	src = (unsigned short *) (data);
	dest = (unsigned long *) [bitmap bitmapData];
	dstRowBytes = [bitmap bytesPerRow];
	
	int i, end = width*height;
	for (i=0; i<end; i++)
	{
		unsigned short *pixel = src;
		unsigned long destPixel = RGB565toRGBA(*pixel);
		
		*dest = destPixel;
		
		dest++;
		src++;
	}
	
	image = [[[NSImage alloc] initWithSize:NSMakeSize(width, height)] autorelease];
	[image addRepresentation:bitmap];
	[bitmap release];
	
	return image;
}


----

I've fixed your routine.  It was close, but not quite right.

    
unsigned long RGB565toRGBA(unsigned short rgb565)
{
  unsigned short temp = CFSwapInt16LittleToHost(rgb565); // swap bytes. may not be needed depending on your RGB565 data
  unsigned long int red, green, blue; // Assuming >=32-bit long int.  uint32_t, where art thou?
  red = (temp >> 11) & 0x1F;
  green = (temp >> 5) & 0x3F;
  blue = (temp & 0x001F);    
  red = (red << 3) | (red >> 2);
  green = (green << 2) | (green >> 4);
  blue = (blue << 3) | (blue >> 2);
  return (red << 24) | (green << 16) | (blue << 8) | 0xFF;
}


----

How about going the opposite direction? I need the ability to go from an NSImage to RGBA bitmap (also 565). I've seen code that uses TIFF as a intermediate step but it doesn't seem there is a clean way to assure there is always the alpha channel. Or at least I seem to be missing it...

----
Adding to the routine above, this worked for me for Big-Endian raw data on an Intel Mac:

    
uint32_t RGB565toRGBA(uint16_t rgb565)
{
	uint16_t temp = CFSwapInt16BigToHost(rgb565); // swap bytes for Big-Endian (data[0] = 0xRRRRRGGG, data[1] = 0xGGGBBBBB) bitmap
	uint8_t red, green, blue; //these only need to be one byte each
	red = (temp >> 11) & 0x1F;
	green = (temp >> 5) & 0x3F;
	blue = (temp & 0x001F);    
	red = (red << 3) | (red >> 2);
	green = (green << 2) | (green >> 4);
	blue = (blue << 3) | (blue >> 2);	
	return CFSwapInt32BigToHost((red << 24) | (green << 16) | (blue << 8) | 0xFF);
}

