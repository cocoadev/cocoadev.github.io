---
layout: page
title: ConvertNSImageToCGImage
---

  I've been searching for a simple way to convert an NSImage into a CGImage(CoreGraphics) but couldn't find anything. So this is the neatest way I could figure via the toll free bridging functions of NSData and CFData. 

    
    NSImage* image = [[NSImage alloc] initByReferencingFile: filename]
	NSData* cocoaData = [NSBitmapImageRep TIFFRepresentationOfImageRepsInArray: [image representations]];
	CFDataRef carbonData = (CFDataRef)cocoaData;
	CGImageSourceRef imageSourceRef = CGImageSourceCreateWithData(carbonData, NULL);
	     // instead of the NULL above, you can fill a CFDictionary full of options
	     // but the default values work for me
	cgImage = CGImageSourceCreateImageAtIndex(imageSourceRef, 0, NULL);
	     // again, instead of the NULL above, you can fill a CFDictionary full of options if you want


---- What about different representations, can those be saved?
----
Might want to add a release of the imageSourceRef after you have the image... *seems* to fix a leak that 'leaks' was complaining about... i.e.,

    
			CFRelease( imageSourceRef );
			imageSourceRef = NULL;


----

Bear in mind that an NSImage and CGImage are not exactly equivalent objects. An NSImage encapsulates a whole variety of formats in a "black box" fashion, including vector images (e.g. PDF). A CGImage is always a bitmap wrapped around a block of memory containing pixels. Therefore converting to a CGImage always involves rasterizing an image, if it is not in that form already. Code I have sometimes used is given below. It's more involved than that above, and always converts to a 32-bit RGBA format, but it's often useful. --GC

    

@implementation NSImage (CGImageConversion)

- (NSBitmapImageRep*)	bitmap
{
	// returns a 32-bit bitmap rep of the receiver, whatever its original format. The image rep is not added to the image.
	
	NSSize size = [self size];

	int rowBytes = ((int)(ceil(size.width)) * 4 + 0x0000000F) & ~0x0000000F; // 16-byte aligned

	NSBitmapImageRep *imageRep = [[NSBitmapImageRep alloc] initWithBitmapDataPlanes:nil 
														pixelsWide:size.width 
														pixelsHigh:size.height 
														bitsPerSample:8 
														samplesPerPixel:4 
														hasAlpha:YES 
														isPlanar:NO 
														colorSpaceName:NSCalibratedRGBColorSpace 
														bitmapFormat:NSAlphaNonpremultipliedBitmapFormat 
														bytesPerRow:rowBytes 
														bitsPerPixel:32];
														
	if ( imageRep == NULL )
		return NULL;
	
	NSGraphicsContext* imageContext = [NSGraphicsContext graphicsContextWithBitmapImageRep:imageRep];
	
	[NSGraphicsContext saveGraphicsState];
	[NSGraphicsContext setCurrentContext:imageContext];
	
	[self drawAtPoint:NSZeroPoint fromRect:NSZeroRect operation:NSCompositeCopy fraction:1.0];
	
	[NSGraphicsContext restoreGraphicsState];
	
	return [imageRep autorelease];
}


static void BitmapReleaseCallback( void* info, const void* data, size_t size )
{
	NSBitmapImageRep* bm = (NSBitmapImageRep*)info;
	[bm release];
}



- (CGImageRef)		cgImage
{
	NSBitmapImageRep*	bm = self bitmap] retain]; // data provider will release this
	int					rowBytes, width, height;
	
	rowBytes = [bm bytesPerRow];
	width = [bm pixelsWide];
	height = [bm pixelsHigh];

	[[CGDataProviderRef provider = CGDataProviderCreateWithData( bm, [bm bitmapData], rowBytes * height, BitmapReleaseCallback );
	CGColorSpaceRef colorspace = CGColorSpaceCreateWithName( kCGColorSpaceGenericRGB );
	CGBitmapInfo	bitsInfo = kCGImageAlphaLast;
	
	CGImageRef img = CGImageCreate( width, height, 8, 32, rowBytes, colorspace, bitsInfo, provider, NULL, NO, kCGRenderingIntentDefault );
	
	CGDataProviderRelease( provider );
	CGColorSpaceRelease( colorspace );
	
	return img;
}

@end



Update: Now Leopard is public, the following may be of interest:

NSBitmapImageRep - Creating from a CGImage, Getting a CGImage

AppKit also includes a new initializer method for creating an NSBitmapImageRep from a Quartz CGImage:
    - (id)initWithCGImage:(CGImageRef)cgImage;
An NSBitmapImageRep that is created in this way retains the given CGImage as its primary underlying representation, reflecting the CGImage's properties as its own and using the CGImage to draw when asked to draw.

Since the CGImageRef is simply retained by the NSBitmapImageRep, its resident image data is referenced instead of being copied. If the NSBitmapImageRep is asked for its pixel data (via a -bitmapData or -getBitmapDataPlanes: message), it will oblige by unpacking a copy of the CGImage's content to an internal buffer. The resultant pixel data should be considered read-only. Changing it through the returned pointer(s) will not cause the CGImage to be re-created.

Regardless of how it was created, an NSBitmapImageRep can now be asked to return an autoreleased CGImage representation of itself:
    - (CGImageRef)CGImage;
Using this method may cause the CGImage to be created, if the NSBitmapImageRep does not already have a CGImage representation of itself. Once created, the CGImage is owned by the NSBitmapImageRep, which is responsible for releasing it when the NSBitmapImageRep itself is deallocated.
----
Why are NSBitmapImageRep and CGImage gratuitously different ?

When Apple was creating Core Graphics after the aquisition of NeXT, they obviously already had NSBitmapImageRep at their disposal.  They could have either implemented CGImage using NSBitmapImageRep or reimplemented NSBitmapImageRep using CGImage or even toll-free bridged CGImage and NSBitmapImageRep.

Why do we have to copy the data out of an NSBitmapImageRep to create a CGImage and visa versa.  Why do they store image data differently ?

If the code above is used with an NSImage that already has an NSBitmapImageRep, two more copies of the image data are created just to get a flippin CGImage!  One of the copies is then discarded.  Come on!  This is absurd.

----
The two are considerably different, and not just gratuitously.

NSBitmapImageRep is a simple wrapper around a blob of bitmap data. It has a single region of in-memory bitmap data (multiple regions if it's planar) and then various metadata about that bitmap, such as the pixel format, rowbytes, etc.

CGImage is a more complex wrapper around more conceptual image data. This data does not have to be bitmap data, and it does not have to be in memory.

In short, CGImage is an abstract raster image container with many capabilities which are more akin to NSImage than NSBitmapImageRep. It serves different needs for different purposes.

If you don't like the redundancy of creating a second copy if your NSImage has an NSBitmapImageRep, might I suggest that you special-case the the code to use the existing bitmap rep if there is one? -- MikeAsh
----
Right above on this very page, the oposite is stated: "Bear in mind that an NSImage and CGImage are not exactly equivalent objects. An NSImage encapsulates a whole variety of formats in a "black box" fashion, including vector images (e.g. PDF). A CGImage is always a bitmap wrapped around a block of memory containing pixels. Therefore converting to a CGImage always involves rasterizing an image, if it is not in that form already. Code I have sometimes used is given below. It's more involved than that above, and always converts to a 32-bit RGBA format, but it's often useful. --GC"

GCImage is indeed a sub-set of NSBitbapImageRep and not the superset claimed.

----
What are you talking about? I never said that CGImage is equivalent to NSImage, nor did I say that CGImage was a superset of NSBitmapImageRep. CGImage is *different* from those two, while combining capabilities of each. This is why there is no simple shortcut way to go from one to the other. -- MikeAsh

----
*If the code above is used with an NSImage that already has an NSBitmapImageRep, two more copies of the image data are created just to get a flippin CGImage!  One of the copies is then discarded.  Come on!  This is absurd.*

Whatever Apple's reasoning, we are stuck with it. However, the above code is not making two copies - it's making one. The first copy is made only to ensure that whatever the original format of the NSImage (even PDF), the bitmap is in RGBA 32-bit format. The second method wraps the same pixel data in a CGImage without copying it - the bitmap rep's data buffer is simply retained by the second object, so when the first is released, ownership of the same pixel buffer passes to the CGImage. If your image already has a suitable 32-bit RGBA NSBitmapImageRep, you can use that "as is" without the first copy (though the code as given would need to be refactored slightly - maybe make the second method a category on NSBitmapImageRep, rather than NSImage - which is what Leopard does). --GC

----
Actually this is somewhat wrong; the ownership is not passed, and the CGImage will become invalid when the NSBitmapImageRep is destroyed. To implement this correctly, data needs to be allocated separately and passed to the NSBitmapImageRep, and then a deallocator function needs to be provided to CGDataProviderCreateWithData. -- MikeAsh

----

Yep, I think you may be right - it's a bit hard to follow the docs on this one. On reflection it looks like the pixel buffer may be leaking, but the CGImage doesn't become invalid when the bitmap goes away (it appears to work without any problems as far as rendering the CGImage long after the bitmap has been released anyway). Maybe I'm just getting lucky... I'll have another look at it. Thanks. --GC

Update: I have amended the code above so that the CGImage effectively retains the NSBitmapImageRep (and hence the pixel buffer) it creates, via the data provider. This is then released by the data provider when requested. It's perhaps not the ideal solution but required the smallest change to the code. --GC

----
The overhead of the NSBitmapImageRep compared to the data it contains is very small, so I don't see anything wrong with this solution personally, and it's definitely simpler than managing the backing store yourself. -- MikeAsh

----
For whatever reason, the code above was telling me:

<Error>: CGBitmapContextCreate: unsupported parameter combination: 8 integer bits/component; 32 bits/pixel; 3-component colorspace; kCGImageAlphaLast; 1872 bytes/row.


I fixed it by changing the bitmap format to not specify that the alpha is premultiplied (bitmapFormat:0):


    
	NSBitmapImageRep *imageRep = [[NSBitmapImageRep alloc] initWithBitmapDataPlanes:nil 
				pixelsWide:size.width 
				pixelsHigh:size.height 
				bitsPerSample:8 
				samplesPerPixel:4 
				hasAlpha:YES 
				isPlanar:NO 
				colorSpaceName:NSCalibratedRGBColorSpace 
				bitmapFormat:0 
				bytesPerRow:rowBytes 
				bitsPerPixel:32];


I was running the code under Leopard, if that makes any difference.

