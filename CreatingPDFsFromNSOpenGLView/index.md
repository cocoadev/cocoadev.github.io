---
layout: page
title: CreatingPDFsFromNSOpenGLView
---



I'm writing an application wich plots graphics, and one of its features is saving graphics as PDF files. 2D graphics are drawn in a subclass of NSView and I use NSView's dataWithPDFInsideRect: method to create the PDF. It works perfectly. But it doesn't work with NSOpenGLView. When  calling an NSOpenGLView's dataWithPDFInsideRect: method (since NSOpenGLView is a subclass of NSView i thought it would work) only get a PDF file with no drawing. 

Anybody knows how to create a PDF from a NSOpenGLView?

Thanks in advance

Angel David Puerto

----

I don't know if there's any magic to de-obfusticate NSOpenGLView, but if you can make a PDF from an image, I can give you code to generate an NSImage from an NSOpenGLView ( I've done a lot of opengl-to-quicktime code lately ).

--ShamylZakariya

----

That would be great!! Could you send it to me? 
Thank you very much.

Angel David Puerto

----

I spoke hastily above. The thing is that recently I optimized away the NSImage stuff for a later stage in my movie encoding. Now I serialize my opengl buffer into a huge flat (RAW) file in tmp and later sequentially extract NSImages from it. I could give you code for both sides and you could figure out the rest. But, instead I'll hack it together into something untested and unlikely to work:


    

/*
    'source' is your NSOpenGLView
*/

//width needs to be divisible by four
NSSize imageSize = [source bounds].size;			
imageSize.width = ( imageSize.width - ( ((int)imageSize.width) % 4 ));

int imageWidth = imageSize.width;
int imageHeight = imageSize.height;
int bytesPerPixel = 4;
int bytesPerImage = imageWidth * imageHeight * bytesPerPixel;
char *imageBuffer = malloc( bytesPerImage );

	
/*
	Now, extract image bytes
*/  
		
source openGLContext] makeCurrentContext];
glReadPixels(0, 0, imageWidth, imageHeight, GL_RGBA, GL_UNSIGNED_BYTE, imageBuffer);


/*
	Now, make 24bit image rep
*/

[[NSBitmapImageRep *rep = [[NSBitmapImageRep alloc]
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
free( imageBuffer );

return flipped;




Rest assured there are bugs in that code -- I just stitched it together from my code, leaving out the part where the buffer is saved to disk and then chunks are read from it to fill the bitmap image rep.

Note, if the 32 to 24-bit conversion seems odd, I did it because it's INCREDIBLY faster on my machine, since obviously OpenGL is internally using 32 bit graphics regardless of the pixel format specifying 24. I don't know anything about pre-multiplying alpha in an image, so it's easier for me to create a non-alpha channel bitmap rep and just copy over the color bits and skip the alpha from the image buffer.

Anyway, I bet you can adapt this.

Also, before anybody hollers about my nasty coding styles above -- I reiterate, it's a stitch job from several places in my program ( e.g., I've got an OpenGL frame grabber interface, and an ImageSource interface, and so on -- it's all very DesignPattern and extensible... ); anyway, the above is about as pretty as one can expect in such a situation.

--ShamylZakariya

----

Thank you, ShamylZakariya. I used your code. It works perfectly, and was very easy to adapt.
Thank you very much.

Angel David Puerto

----

Fantastic! I'm glad to be of service. --ShamylZakariya

----

As ShamylZakariya said, there are bugs in that code.  Here is a working version, that also doesn't require the image to be flipped.  Note that I have purposefully not tried to optimize this for speed, valuing clarity instead.
This is meant to be called from the NSOpenGLView subclass, but could be easily adapted to operate *upon* a NSOpenGLView subclass.

    
- (NSImage *)copyAsNSImage
{
	NSSize imageSize = [self bounds].size;
	int imageWidth = imageSize.width;
	int imageHeight = imageSize.height;
	int bytesPerPixel = 3;

	int rowSizeBytes = imageWidth * bytesPerPixel;
	// row size (in bytes) must be divisible by four, because openGL pads the image buffer rows to a 4-byte alignment
	if ((rowSizeBytes % 4 ) > 0) {
		rowSizeBytes -= (rowSizeBytes % 4 );
		rowSizeBytes += 4;
	}
	
	long bytesPerImage = rowSizeBytes * imageHeight;
	unsigned char *imageBuffer = malloc( bytesPerImage);

	/*
	 Now, extract image bytes
	 */  
	
	self openGLContext] makeCurrentContext];
	glReadPixels(0, 0, imageWidth, imageHeight, GL_RGB, GL_UNSIGNED_BYTE, imageBuffer);
	
	/*
	 Now, make 24bit image rep
	 */
	
	[[NSBitmapImageRep *rep = [[NSBitmapImageRep alloc]
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
	
	unsigned char *src = imageBuffer;
	
	unsigned char *srcPixel;
	float r, g, b;
	int row, col;
	NSColor *theColor;
	for (row = 0; row < imageHeight; row++) {
		for (col = 0; col < imageWidth; col++) {
			srcPixel = src + (row * rowSizeBytes) + (col * bytesPerPixel);
			r = ((float) *srcPixel / UCHAR_MAX);  srcPixel++;
			g = ((float) *srcPixel / UCHAR_MAX);  srcPixel++;
			b = ((float) *srcPixel / UCHAR_MAX);  srcPixel++;
			theColor = [NSColor colorWithCalibratedRed:r green:g blue:b alpha:1.0];
			[rep setColor:theColor atX:col y:(imageHeight - row - 1)];
		}
	}

	
	NSImage *image = [[NSImage alloc] init];
	[image addRepresentation:rep];
	
	/*
	 Free up temporaries
	 */
	[rep release];
	free( imageBuffer );
	
	return image;
}


Hope this is useful to others.

-Dave Hirsch

