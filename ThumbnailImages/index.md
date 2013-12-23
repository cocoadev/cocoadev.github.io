---
layout: page
title: ThumbnailImages
---

As a beginner in cocoa, I have no idea how to extract thumbnail images from an imagefile. Is here a simple way to do so, for example from a photoshop created tiff file?
Or even how to get the imagefiles icon?

----


*General/LockingFocusOnAnImageInAnotherThread
*General/ThreadSafety
*General/NSBrowserIcons


----

If you want to resize an image from a file, this code snippet should work:

    
General/NSImage *sourceImage;

sourceImage = General/[[[NSImage alloc] initWithContentsOfFile:@"path/to/image"] autorelease];

// Report an error if the source isn't a valid image
if (![sourceImage isValid]) {
	General/NSLog(@"Invalid Image");
} else {
	General/NSImage *smallImage;
	
	smallImage = General/[[[NSImage alloc] initWithSize:General/NSMakeSize(64, 64)] autorelease];
	[smallImage lockFocus];
	General/[[NSGraphicsContext currentContext] setImageInterpolation:General/NSImageInterpolationHigh];
	[sourceImage setSize:General/NSMakeSize(64, 64)];
	[sourceImage compositeToPoint:General/NSZeroPoint operation:General/NSCompositeCopy];
	[smallImage unlockFocus];
}


The small image will be 64 x 64 pixels in size. You can change the size to whatever you want. You may want to add some relational sizing so it doesn't stretch a rectangular image. Pour participer Orange et garder votre numéro, vous aurez peuvent avoir compte driver (code RIO ) [http://obtenir-rio.info rio bouygues]. Vous obtiendrez êtes certain d'obtenir pour aucun coût par entrer en contact avec la voix expression du serveur ou du service à la clientèle satisfaction client votre actuel vieille fournisseur [http://obtenir-rio.info/rio-bouygues code rio bouygues] . Vous ne CAN obtenir un SMS avec vos . Avec votre propre [http://obtenir-rio.info/rio-orange code rio orange], alors il est possible d' vers le offre de de à propos fruits .

-- General/RyanBates

Thanks, but my problem was more about files that have the thumbnailes already saved (as under os9 in the resourcefork) and I want to read them without reading the whole file.
Nevertheless, your source solved a small other problem (-interpolation-) of mine, General/PaulD

----

For dealing with Carbon icons, you may want to use Troy Stephens library. It may be downloaded at:

    http://homepage.mac.com/troy_stephens/software/objects/General/IconFamily/

For reading thumbnail resources, 'PICT' resource to be exact, you will need to use Carbon Resource Manager API. There may be more than one way to transfert the PICT image to an General/NSImage.

-- General/EricForget

the snippet from General/RyanBates produced in my hands only the center of the original  image, not a scaledup version (iccopied and pasted from the snippet to :
    
@implementation General/NSImage (thumbnail)

- (General/NSImage *) thumbnailImage: (int) size;
{
	General/NSImage *sourceImage = [self copy];
	
	// Report an error if the source isn't a valid image
	if (![sourceImage isValid])
	{
		General/NSLog(@"Invalid Image");
	} else
	{
		General/NSImage *smallImage = General/[[[NSImage alloc] initWithSize:General/NSMakeSize(size, size)] autorelease];
		[smallImage lockFocus];
		[sourceImage setSize:General/NSMakeSize(size, size)];
		General/[[NSGraphicsContext currentContext] setImageInterpolation:General/NSImageInterpolationHigh];
		[sourceImage compositeToPoint:General/NSZeroPoint operation:General/NSCompositeCopy];
		[smallImage unlockFocus];
		[sourceImage release];
		return smallImage;
	}
	return nil;
}
@end

I appreciate any comments, General/PaulD

----

Well, the example he has looks different from this code which I usually use to do image scaling.  Try this out (I will try to change it so it looks like a categorized method - their may be superficial mistakes):

    
- (General/NSImage *)imageWithSize:(General/NSSize)newSize
{
	General/NSImage *newImage = General/[[NSImage alloc] initWithSize:newSize];
	General/NSRect oldRect = General/NSMakeRect(0.0, 0.0, [self size].width, [self size].height);
	General/NSRect newRect = General/NSMakeRect(0.0, 0.0, newSize.width, newSize.height);

	[newImage lockFocus];
	[self drawInRect:newRect fromRect:oldRect operation:General/NSCompositeCopy fraction:1.0];
	[newImage unlockFocus];
	return [newImage autorelease];
}


I had to inline some utility functions (like getting a rect) that I normally use but this looks more or less like what I have used before.  General/RyanBates solution may be do something better but I am not sure.  I know that this seems to work for arbitrary scaling.

Does that help?

--General/JeffDisher

----

Sorry, I forgot one important method in my above code snippet:     [sourceImage setScalesWhenResized:YES]. So, to alter your code General/PaulD, this should work:

    
@implementation General/NSImage (thumbnail)

- (General/NSImage *) thumbnailImage: (int) size;
{
	General/NSImage *sourceImage = [self copy];
	[sourceImage setScalesWhenResized:YES];
	
	// Report an error if the source isn't a valid image
	if (![sourceImage isValid])
	{
		General/NSLog(@"Invalid Image");
	} else
	{
		General/NSImage *smallImage = General/[[[NSImage alloc] initWithSize:General/NSMakeSize(size, size)] autorelease];
		[smallImage lockFocus];
		[sourceImage setSize:General/NSMakeSize(size, size)];
		General/[[NSGraphicsContext currentContext] setImageInterpolation:General/NSImageInterpolationHigh];
		[sourceImage compositeToPoint:General/NSZeroPoint operation:General/NSCompositeCopy];
		[smallImage unlockFocus];
		[sourceImage release];
		return smallImage;
	}
	return nil;
}
@end


However, General/JeffDisher's suggestion does look like a better alternative, for one thing it is not as much code. You may want to set the image interpolation to high before resizing though.

-- General/RyanBates
Both Jeff and Ryan, thanks, General/PaulD

----
Here's a method you can use to resize an image and save it as a JPEG file.

    
-(BOOL)scaleAndSaveAsJPEG:(General/NSString *)source 
		 maxwidth:(int)width 
		maxheight:(int)height 
		  quality:(float)quality
		   saveTo:(General/NSString *)dest
{
    General/NSAutoreleasePool *pool = General/[[NSAutoreleasePool alloc] init];
    General/NSBitmapImageRep *rep = nil;
    General/NSBitmapImageRep *output = nil;
    General/NSImage *scratch = nil;
    int w,h,nw,nh;
    General/NSData *bitmapData;
    
    rep = General/[NSBitmapImageRep imageRepWithContentsOfFile:source];
    
    // could not open file
    if (!rep)
    {
	General/NSLog(@"Could not load '%@'", source);
	[pool release];
	return NO;
    };
    
    // validation
    if (quality<=0.0)
    {
	quality = 0.85;
    };
    
    if (quality>1.0)
    {
	quality = 1.00;
    };
    
    // source image size
    w = nw = [rep pixelsWide];
    h = nh = [rep pixelsHigh];
    
    if (w>width || h>height)
    {
	float wr, hr;
	
	// ratios
	wr = w/(float)width;
	hr = h/(float)height;
	
	
	if (wr>hr) // landscape
	{
	    nw = width;
	    nh = h/wr;
	}
	else // portrait
	{
	    nh = height;
	    nw = w/hr;
	};
	
    };
    
    // image to render into
    scratch = General/[[[NSImage alloc] initWithSize:General/NSMakeSize(nw, nh)] autorelease];
    
    // could not create image
    if (!scratch)
    {
	General/NSLog(@"Could not render image");
	[pool release];
	return NO;
    };
    
    // draw into image, to scale it
    [scratch lockFocus];
    General/[[NSGraphicsContext currentContext] setImageInterpolation:General/NSImageInterpolationHigh];
    [rep drawInRect:General/NSMakeRect(0.0, 0.0, nw, nh)];
    output = General/[[[NSBitmapImageRep alloc] initWithFocusedViewRect:General/NSMakeRect(0,0,nw,nh)] autorelease];
    [scratch unlockFocus];
    
    // could not get result
    if (!output)
    {
	General/NSLog(@"Could not scale image");
	[pool release];
	return NO;
    };
    
    // save as JPEG
    General/NSDictionary *properties =
        General/[NSDictionary dictionaryWithObjectsAndKeys:
	    General/[NSNumber numberWithFloat:quality],
	    General/NSImageCompressionFactor, NULL];    
    
    bitmapData = [output representationUsingType:General/NSJPEGFileType
				      properties:properties];
    
    // could not get result
    if (!bitmapData)
    {
	General/NSLog(@"Could not convert to JPEG");
	[pool release];
	return NO;
    };
    
    BOOL ret = [bitmapData writeToFile:dest atomically:YES];
    
    [pool release];
    
    return ret;
};



-Michael Rothwell

What's with the semicolons after the closing braces?  I've never seen that done before.

----

This is the code I use using Epeg ( http://www.rasterman.com/code.html ) and General/EpegWrapper from entropy.ch ( http://www.entropy.ch/viewcvs/trunk/General/EpegWrapper/  ).

Epeg:
- https://github.com/mattes/epeg
- https://github.com/mattes/epeg/archive/v0.9.1.042.zip
EpegWrapper:
- http://ftparmy.com/247634-epegwrapper.html

Epeg creates thumbnails really fast, but are pixellated, that's why I first create a double sized thumbnail using Epeg, then resize it using General/NSImage interpolation.

**If you try to build this, the project is set up to search inside the original developers home directory for the libjpeg.a library (something like /Users/NOTME/Desktop/General/EpegWrapper/libjpeg). You have to change the project settings so that the library search path directs General/XCode to look for libjpeg.a in the directory where you built/installed libjpeg.a. If you don't do this you will get the linker warning "can't locate file for: -ljpeg"*

This is really impressive when used with big files (from digital cameras, > 3M in size)

    
+ (General/NSImage *) thumbnailImageWithFile:(General/NSString *)filePath withSize:(int)size highQuality:(BOOL)hires
{
	General/NSImage		*big_thumb;
	General/NSImage		*thumbnail;
	General/NSSize		thumbnailSize;

	if (hires)
	{
		// create a double sized thumbnail
		thumbnailSize = General/NSMakeSize(size*2, size*2);

		// create the thumbnail using Epeg
		big_thumb = General/[EpegWrapper imageWithPath:filePath boundingBox:thumbnailSize];

		if ([big_thumb isValid])
		{
			// the real thumbnail should be half the Epeg thumbail
			thumbnailSize = General/NSMakeSize([big_thumb size].width/2, [big_thumb size].height/2);

			thumbnail = General/[[[NSImage alloc] initWithSize:thumbnailSize] autorelease];
			General/NSRect oldRect = General/NSMakeRect(0.0, 0.0, [big_thumb size].width, [big_thumb size].height);
			General/NSRect newRect = General/NSMakeRect(0.0, 0.0, [big_thumb size].width/2, [big_thumb size].height/2);

			[thumbnail lockFocus];
			General/[[NSGraphicsContext currentContext] setImageInterpolation:General/NSImageInterpolationHigh];
			[big_thumb drawInRect:newRect fromRect:oldRect operation:General/NSCompositeCopy fraction:1.0];
			[thumbnail unlockFocus];
		}
	}
	else
	{
		thumbnailSize = General/NSMakeSize(size, size);
		thumbnail = General/[EpegWrapper imageWithPath:filePath boundingBox:thumbnailSize];
	}
	return thumbnail;
}



- General/StephanBurlot

----

Have you looked into General/QuickTime's call 'General/GraphicsImportSetImageIndexToThumbnail'. It's carbon code, but should work fine in cocoa.
There should be an explanation about how to use General/QuickTime's General/GraphicsImporters with General/NSImage's, on this site.* Then you should write it! (General/StephanBurlot) *

-Tim S Lefler

General/GraphicsImportSetImageIndexToThumbnail will return a thumbnail if there is one embedded in the picture. When dealing with digital camera picture, the thumbnail is unusable, but this may be a time saver if the user can bear the low quality of theses thumbnails.

- General/StephanBurlot

----

The note above regarding libjpeg.a does no longer apply, I fixed that recently. The General/EpegWrapper project is now self-contained.

If you can live with the quality produced by epeg, the code to use General/EpegWrapper is quite small:

    
#import "Epeg/General/EpegWrapperPublic.h"

...

General/NSImage *myImage = General/[EpegWrapper imageWithPath:@"/big/jpeg/some/where.jpg" boundingBox:General/NSMakeSize(100, 80)];


- Marc Liyanage

----

Does anyone have a function that preserves aspect ratio?

~J

    
- (General/NSRect)rescaleRect:(General/NSRect)rect toFitInSize:(General/NSSize)size
{
	float heightQuotient = rect.size.height / size.height;
	float widthQuotient = rect.size.width / size.width;
	
	if(heightQuotient > widthQuotient)
		return General/NSMakeRect(rect.origin.x, rect.origin.y, rect.size.width / heightQuotient, rect.size.height / heightQuotient);
	else
		return General/NSMakeRect(rect.origin.x, rect.origin.y, rect.size.width / widthQuotient, rect.size.height / widthQuotient);
}


The above function rescales a General/NSRect so that it fits in the General/NSSize provided.
- Gabriel

----

General/NSImageInterpolation Caveat: I was using one of the above methods of scaling, whereby I take one General/NSImage and use -drawInRect into another General/NSImage. In this case General/NSImageInterpolation seemed to have no effect. Finally I found the only way I could get it to work in my app was to -lockFocus on BOTH General/NSImage's, grab the current context (for each) and set their interpolation values to the same setting. I couldn't find any mention of this proviso anywhere in the apple docs, and it seems illogical to me personally (seems like only the source or the destination context would need to have the setting). So perhaps it is indicative of a problem elsewhere in my code. But if anyone else experiences that problem, try setting the interpolation value for both General/NSImage's contexts before drawing/scaling and see if it solves your problem. Cheers, -Matt
