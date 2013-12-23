---
layout: page
title: MemoryNotBeingFreed
---

I've written some code which builds a quicktime movie ( and/or exports an image sequence ) from frames grabbed from OpenGL. The recording mechanism saves the framebuffer, raw, to a cache file in NSTemporaryDirectory and that works just fine.

The encoding phase extracts images one by one, flips them vertically, and then does whatever ( e.g., adding to the quicktime movie or saving to a numbered image in a sequence ). The encoding phase is threaded, performed by a fire and forget thread context as such:

    
- (void) saveSequenceToPath: (NSString *) path
{
	[NSThread detachNewThreadSelector: @selector(saveSequenceOnThread:) 
              toTarget:self withObject:path];	
}


Where     saveSequenceOnThread:  is implemented as such:

    
- (void)saveSequenceOnThread: (id) data
{
	NSAutoreleasePool *pool = [[NSAutoreleasePool alloc] init];  
	NSString *path = ( NSString * ) data;
	
	sequencing = YES;
	abortSequencing = NO;
	sequenceProgress = 0.0f;

	/*
		Now, for each image in imageSource, save as a .PNG to path
	*/
	
	[imageSource beginExtraction];
	
	int numImages = [imageSource numFrames], i;
	for ( i = 0; i < numImages; i++ )
	{
		if ( abortSequencing )
		{
			break;
		}
	
		NSImage *image = [imageSource nextFrame];
		NSArray *reps = [image representations];
		NSEnumerator *repEnum = [reps objectEnumerator];
		id object;
		
		while ( object = [repEnum nextObject] )
		{
			if ( [object isKindOfClass: [NSBitmapImageRep class]] )
			{
				NSBitmapImageRep *bmp = ( NSBitmapImageRep * ) object;
				NSData *bits = [bmp representationUsingType: NSPNGFileType 
                                  properties: nil];
				NSString *filename = [NSString stringWithFormat: @"%s/image-%04d.png", 
                                  [path cString], i];
				
				[bits writeToFile: filename atomically: NO];
				continue; //don't examine other image reps
			}
		}

                //this is polled by a timer in the main thread
		sequenceProgress = ( (float) i / (float) numImages );		
		[image release]; //clean up
	}
	
	
	[imageSource endExtraction];
	
	sequencing = NO;
	[pool release];
}


The TROUBLE here is that memory consumption grows linearly while the thread executes, sometimes peaking as high as 2 gigs when a long sequence is saved ( or a long movie is encoded ). When the thread terminates, and the NSAutoReleasePool is released, the memory is cleared, completely. 

Commenting out the code that does the *work*, leaving only the calls to      [imageSource nextFrame];  the memory still grows and isn't released until the thread terminates, which implies to me the trouble is in my implementation of my ImageSource protocol which is below.

    

/*
   Called right before extracting images with nextImage. Image dimensions are 
   already known.
*/
- (void) beginExtraction
{
	currentImage = 0;
	cacheFile = fopen( [cacheFileName cString], "r" );

	if ( !cacheFile )
	{
		printf( "[OpenGLSerializer beginExtraction] unable to open cacheFile for reading!\n" );
		return;
	}

	imageBuffer = ( char * ) malloc( bytesPerImage );
}

/*
   Called when done extracting images.
*/
- (void) endExtraction
{
	if ( imageBuffer )
	{
		free( imageBuffer );
		imageBuffer = 0;
	}
}


- (NSImage *) nextFrame
{
	if ( !cacheFile ) return nil;
	
	if ( currentImage < numImages )
	{
		/*
			Current code can't handle the 4-bytes-per-pixel code I'm pumping
			above. Will have to do this in a few steps:
				allocate image buffer to hold 4bpp image ( done in beginExtraction )
				allocate NSBitmapImageRep for 3bpp image ( done locally )
				copy first 3 bytes (RGB), for each pixel skipping alpha
		*/

		size_t bRead = fread( imageBuffer, bytesPerImage, 1, cacheFile );
		if ( bRead < 1 )
		{
			return nil;
		}
	
		/*
			Create container bitmap
		*/
		NSBitmapImageRep *rep = [[NSBitmapImageRep alloc]
		initWithBitmapDataPlanes:nil
					  pixelsWide:imageWidth
					  pixelsHigh:imageHeight
				   bitsPerSample:8
				 samplesPerPixel:3
						hasAlpha:NO
						isPlanar:NO
				  colorSpaceName:NSCalibratedRGBColorSpace
					 bytesPerRow:0
					bitsPerPixel:0];
					
		/*
			Copy RGB over, but not alpha
		*/

		unsigned char *src, *end, *dest;
		src = imageBuffer;
		end = src + bytesPerImage;
		dest = [rep bitmapData];

		while ( src < end )
		{
			*dest = *src; dest++; src++; //R
			*dest = *src; dest++; src++; //G
			*dest = *src; dest++; src++; //B
			++src;                       //A
		}

		
		NSImage *image = [[NSImage alloc] init];
		[image addRepresentation:rep];

		/*
			Flip image vertically
		*/
		[image setFlipped:YES];
		[image lockFocusOnRepresentation:rep];
		[image unlockFocus];


		/*
			The original NSImage loses its NSBitmapImegreRep when its flipped, and the
			only way I can come up with to get an NSBitmapImageRep back is to make a 
			new NSImage from the original. The horror!
		*/
		NSImage *flipped = [[NSImage alloc] initWithData: [image TIFFRepresentation]];
		
		/*
			Free up temporaries
		*/
		[rep release];
		[image release];
		
		currentImage++;
		return flipped; // released by caller
	}

	return nil;
}



As far as I can tell, I'm freeing up everything properly, though I might have missed something.

What I gather from this is that NSAutoReleasePool doesn't really clean up memory until its released, itself. This is unacceptable! *It's unacceptable for NSAutoreleasePool to work as documented?* Is there a message that can be sent to force NSAutoReleasePool to free pending frees immediately? *Yes.     [pool release]*

Please, if anybody can help I'd appreciate it. This is disturbing.

For what it's worth, I still get the memory leak when the thread does nothing except extract and then free the images. For example:

    

- (void)saveSequenceOnThread: (id) data
{
	NSAutoreleasePool *pool = [[NSAutoreleasePool alloc] init];  

	[imageSource beginExtraction];	
	int numImages = [imageSource numFrames], i;

	for ( i = 0; i < numImages; i++ )
	{
		NSImage *image = [imageSource nextFrame];
		[image release];
	}
	
	
	[imageSource endExtraction];
	[pool release];
}



--ShamylZakariya

for the solution to this problem, and the ensuing discussion, see NSAutoreleasePool

