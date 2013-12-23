---
layout: page
title: CIImage
---

A CIImage object represents an immutable image in CoreImage.

It is tempting to use NSBitmapImageRep in the following manner to build a CIImage.
    
NSBitmapImageRep * bitmap = ... //bitmap want to convert
CIImage *initialCIImage = [[CIImage alloc] initWithBitmapImageRep:bitmap];

Be warned, this seems to be unreliable. In particular it seems that the internal bytesPerRow is not a multiple of 16 and
then in some circumstances artifacts like skewing occur (the docs only recommend 16 but don't say it is compulsary - possible Apple bug?)

I've been using the following code with success to draw into a bitmap for use in CIImage -- RbrtPntn
    
NSRect rect = ... the (0,0,width,height) bounds of the final image

// Build an offscreen CGContext
int bytesPerRow = rect.size.width*4;			//bytes per row - one byte each for argb
bytesPerRow += (16 - bytesPerRow%16)%16;		// ensure it is a multiple of 16
size_t byteSize = bytesPerRow * rect.size.height;
void * bitmapData = malloc(byteSize); 
//bzero(bitmapData, byteSize); //only necessary if don't draw the entire image
	
CGColorSpaceRef colorSpace = CGColorSpaceCreateWithName(kCGColorSpaceGenericRGB); 
CGContextRef cg = CGBitmapContextCreate(bitmapData,
	rect.size.width,
	rect.size.height,
	8, // bits per component
	bytesPerRow,
	colorSpace,
	kCGImageAlphaPremultipliedFirst); //later want kCIFormatARGB8 in CIImage

// Ensure the y-axis is flipped
CGContextTranslateCTM(cg, 0, rect.size.height);	
CGContextScaleCTM(cg, 1.0, -1.0 );
CGContextSetPatternPhase(cg, CGSizeMake(0,rect.size.height)); 
	
// Draw into the offscreen CGContext
[NSGraphicsContext saveGraphicsState];
NSGraphicsContext * nscg = [NSGraphicsContext graphicsContextWithGraphicsPort:cg flipped:NO];
[NSGraphicsContext setCurrentContext:nscg];
		
	// Here is where you want to do all of your drawing...
	[[NSColor blueColor] set];
	[NSBezierPath fillRect:rect];
	// etc, etc...

[NSGraphicsContext restoreGraphicsState];
CGContextRelease(cg);

// Extract the CIImage from the raw bitmap data that was used in the offscreen CGContext
CIImage * coreimage = [[CIImage alloc] 
	initWithBitmapData:[NSData dataWithBytesNoCopy:bitmapData length:byteSize] 
	bytesPerRow:bytesPerRow 
	size:CGSizeMake(rect.size.width, rect.size.height) 
	format:kCIFormatARGB8
	colorSpace:colorSpace];
	
// Housekeeping
CGColorSpaceRelease(colorSpace); 
	
return coreimage;


----
I'm having a lot of trouble making a simple NSImage black and white using CoreImage (I'm new to it), and turning the NSImage to a CIImage is the issue. Here is my drawRect code segment:

    
	CIContext *bwimage;
	CIImage *resimage, *ciresult;
	CIFilter *bwfilter;
...
	if (isBlackAndWhite) {
		bwimage = [[NSGraphicsContext currentContext] CIContext];
		bwfilter = [CIFilter filterWithName:@"CIColorMonochrome"];
		[bwfilter setDefaults];
		ciresult = [CIImage imageWithData:self image] [[TIFFRepresentation]];
		[bwfilter setValue:ciresult forKey:@"inputImage"];
		[bwfilter setValue:[CIColor colorWithRed:0 green:0 blue:0] forKey:@"inputColor"];
		resimage = [bwfilter valueForKey:@"outputImage"];
		[resimage drawAtPoint:NSZeroPoint fromRect:NSZeroRect operation:NSCompositeSourceOver fraction:1];
	}


This does nothing.

If I use the above function code:

    
// code by Robert Pointon
- (CIImage *)coreImageResult
{
	// CODE BY PIETRO GAGLIARDI MODIFYING ORIGINAL
	NSRect rect;
	
	rect.origin = NSZeroPoint;
	rect.size = self image] size];
	// END CODE
	// Build an offscreen [[CGContext
	int bytesPerRow = rect.size.width*4;			//bytes per row - one byte each for argb
	bytesPerRow += (16 - bytesPerRow%16)%16;		// ensure it is a multiple of 16
	size_t byteSize = bytesPerRow * rect.size.height;
	void * bitmapData = malloc(byteSize); 
	//bzero(bitmapData, byteSize); //only necessary if don't draw the entire image
		
	CGColorSpaceRef colorSpace = CGColorSpaceCreateWithName(kCGColorSpaceGenericRGB); 
	CGContextRef cg = CGBitmapContextCreate(bitmapData,
		rect.size.width,
		rect.size.height,
		8, // bits per component
		bytesPerRow,
		colorSpace,
		kCGImageAlphaPremultipliedFirst); //later want kCIFormatARGB8 in CIImage

	// Ensure the y-axis is flipped
	CGContextTranslateCTM(cg, 0, rect.size.height);	
	CGContextScaleCTM(cg, 1.0, -1.0 );
	CGContextSetPatternPhase(cg, CGSizeMake(0,rect.size.height)); 
		
	// Draw into the offscreen CGContext
	[NSGraphicsContext saveGraphicsState];
	NSGraphicsContext * nscg = [NSGraphicsContext graphicsContextWithGraphicsPort:cg flipped:NO];
	[NSGraphicsContext setCurrentContext:nscg];
			
		// Here is where you want to do all of your drawing...
		// BEGIN CODE BY PIETRO GAGLIARDI, REPLACING OLD CODE
		self image] drawInRect:rect fromRect:[[NSZeroRect operation:NSCompositeSourceOver fraction:1];
		// END CODE
		// etc, etc...

	[NSGraphicsContext restoreGraphicsState];
	CGContextRelease(cg);

	// Extract the CIImage from the raw bitmap data that was used in the offscreen CGContext
	CIImage * coreimage = [[CIImage alloc] 
		initWithBitmapData:[NSData dataWithBytesNoCopy:bitmapData length:byteSize] 
		bytesPerRow:bytesPerRow 
		size:CGSizeMake(rect.size.width, rect.size.height) 
		format:kCIFormatARGB8
		colorSpace:colorSpace];
		
	// Housekeeping
	CGColorSpaceRelease(colorSpace); 
		
	return coreimage;
}


and do this:

    
		ciresult = [self coreImageResult];


This also does nothing. What am I doing wrong? - PietroGagliardi

----

