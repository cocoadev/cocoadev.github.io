---
layout: page
title: NSBitmapImageRepFromCGImage
---



I want to convert NSBitmapImageReps from any colour format into grayscale NSBitmapImageReps. To cover the widest possible range of possible input NSBitmapImageRep formats, I am following the advice in the Cocoa drawing guide (Cocoa Drawing Guide > Images > Working with Images > Converting Between Color Spaces) of translating the NSBitmapImageRep into a CGImage. To preserve the highest possible bit depth, the CGContext containing the CGImage was made with floating point precision (kCGBitmapFloatComponents). To conform to my desired target format, the CGBitmapInfo also specified no alpha (kCGImageAlphaNone) and the CGColorSpace was set to kCGColorSpaceGenericGray. Despite this being my first excursion into Quartz, this all seems to work fine.

I then need to recover the CGImage as a NSBitmapImageRep. The Cocoa drawing guide shows how to do this by drawing into an NSImage, thereby creating an NSCachedImageRep. When I try to recover the image into NSBitmapImageRep by getting TIFFRepresentation, it seems to have been turned back into a standard RGBA image. So I tried to use the new NSGraphicsContext method, graphicsContextWithBitmapImageRep. This allows you to create a CGContext graphics context where you can draw directly into a specified NSBitmapImageRep. My code is here (the CGImage is called theCGImage and its coordinates are given in imageRect).

    
NSBitmapImageRep *grayscaleBitmap = [[NSBitmapImageRep alloc] initWithBitmapDataPlanes:nil 
				pixelsWide:imageWidth pixelsHigh:imageHeight  
				bitsPerSample: 8*sizeof(float)
				samplesPerPixel:1 
				hasAlpha:NO
				isPlanar:NO
				colorSpaceName:NSCalibratedWhiteColorSpace  
				bitmapFormat:NSFloatingPointSamplesBitmapFormat 
				bytesPerRow:0
				bitsPerPixel:0];

NSGraphicsContext* aBitmapGC=
		[NSGraphicsContext graphicsContextWithBitmapImageRep:grayscaleBitmap];

CGContextRef imageContext = (CGContextRef)[aBitmapGC graphicsPort];

CGContextDrawImage(imageContext, *(CGRect*)&imageRect, theCGImage);


When I run this, I get a completely black image. The error log tells me:
<code>
CGBitmapContextCreate: unsupported parameter combination: 32 integer bits/component; 32 bits/pixel; 1-component colorspace; kCGImageAlphaNone.
</code>


I think the overall approach is fine, because if I set the NSBitmapImageRep to a more standard format (bitsPerSample 8, bitmapFormat 0), I get a standard 8-bit grayscale representation of the image. So what is it that CGBitmapContextCreate doesn’t like? It worries me that the error log says I’m trying to create 32 integer bits/component rather than a float. One idea I entertained is that the NSBitmapImageRep has to conform to a representation supported by CGImage. In the Quartz 2D Programming Guide (Quartz 2D Programming Guide > Graphics Contexts > Creating a Bitmap Graphics Context > Supported Pixel Formats) it says there is one floating point grayscale format. Rather surprisingly it is described as 128 bits per pixel, 32 bits per component, (kCGImageAlphaNone | kCGBitmapFloatComponents) as though it stores it like a regular RGBA representation, but only using one of the four available floats (or using all three RGB samples set to the same value). But if I try to set up my NSBitmapImageRep to mimic this (bitsPerSample 32, samplesPerPixel 4, bitmapFormat NSFloatingPointSamplesBitmapFormat) I get the error message: 
<code>
Inconsistent set of values to create NSBitmapImageRep.
</code>

Is this a bug in NSBitmapImageRep that can’t properly deal with floating point sample specification, or am I doing something wrong? Or should I be extracting the image data from the CGImage in a different way?

Thanks in advance, JulianBlow

----

I’ve found a different way of getting the data out of the CGImage, by directly exporting the image data as NSData and using this to create an NSBitmapImageRep:

    
NSMutableData* imageData = [NSMutableData data];
CGImageDestinationRef destCG=
			CGImageDestinationCreateWithData((CFMutableDataRef)imageData,
			kUTTypeTIFF,1,NULL);
CGImageDestinationAddImage(destCG, theCGImage, NULL);
CGImageDestinationFinalize(destCG);
	
NSBitmapImageRep *grayscaleBitmap=[[NSBitmapImageRep alloc] initWithData:imageData];
CGImageRelease(theCGImage);
CFRelease(destCG);


This produces a grayscale 32 bit float image as required. I’m still interested in why drawing into the NSBitmapImageRep doesn’t work though.

Julian

