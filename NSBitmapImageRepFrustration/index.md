---
layout: page
title: NSBitmapImageRepFrustration
---

I'm trying to draw into an NSBitmapImageRep all via raw data (testing with RGB, not RGBA), and I'm testing it by simply filling in the bitmap with all black. But it doesn't fill up the entire image, and I have no idea why. Anyone see what's wrong with this code?
    
#import <Cocoa/Cocoa.h>

int main()
{
	NSAutoreleasePool *pool = [[NSAutoreleasePool alloc] init];

	int width = 50, height = 50;
	NSBitmapImageRep *bmp = [[NSBitmapImageRep alloc] initWithBitmapDataPlanes:nil pixelsWide:width pixelsHigh:height bitsPerSample:8 samplesPerPixel:3 hasAlpha:NO isPlanar:NO colorSpaceName:NSCalibratedRGBColorSpace bytesPerRow:0 bitsPerPixel:24];
	
	unsigned char *chars = [bmp bitmapData];
	int i, t = width*height*3;
	for (i=0; i<t; i++)
	{
		chars[i] = 0x00; // black
	}
	
	if (bmp)
	{
		NSImage *img = [[NSImage alloc] initWithSize:NSMakeSize(width, height)];
		[img addRepresentation:bmp];

		img [[TIFFRepresentation] writeToFile:@"/Users/me/Desktop/test.tif" atomically:YES];
		
		[img release];
		[bmp release];
	}
	
	[pool release];
	
	return 0;
}

----

    bytesPerRow:0 should be     bytesPerRow:width * 3 --zootbobbalu

----

Ahh, thank you very much. I had some other code that set it to 0, so that's what I used as default. Works like a charm now :-)

