---
layout: page
title: JPEGsNSImage
---



I am having problems regarding the quality of images I manipulate with NSImage and I would really appreciate some advice...

I have a very high quality JPEG file, which if read into an NSBitmapImageRep, and then saved to a file will be outputted at the same resolution and quality which is great!

BUT, I wish to manipulate this image (i.e. composite other images over it etc. and draw onto) and hence I think I need to use NSImage.

However whenever I use NSImage, no matter what I do the quality of the image is degraded too much. Is there any way to either prevent this from happening or to draw and manipulate an instance of NSBitmapImageRep

For example, when I comment out my NSImage, the code works fine:
    
	[progressMeter startAnimation:self];
	[[NSGraphicsContext currentContext] setImageInterpolation:NSImageInterpolationHigh];

	NSString* str = [[NSString alloc] initWithString:@"~/Desktop/myImage.jpg"];
	str = [str stringByExpandingTildeInPath];

	NSData* data = [[NSData alloc] initWithContentsOfFile:str];
	
	NSBitmapImageRep* image = [[NSBitmapImageRep alloc] initWithData:data];
	[data autorelease];
	
// Commented out code begin ////// If used with NSImage, the quality is affected drastically

/*	NSImage* myImage = [[NSImage alloc] initWithSize:[image size]];

	[myImage addRepresentation:image];
	[myImage lockFocus];
	[NSBezierPath fillRect:NSMakeRect(50,50,100,100)];
	[myImage unlockFocus];
	
	image = [[NSBitmapImageRep alloc] initWithData:[myImage TIFFRepresentation]];
	*/
// Commented out code end //////
	
	NSDictionary *imageProps = [NSDictionary dictionaryWithObject:[NSNumber numberWithFloat:0.9] forKey:NSImageCompressionFactor];
	data = [image representationUsingType:NSJPEGFileType properties:imageProps];
	
	str = [[NSString alloc] initWithString:@"~/Desktop/outImage.jpg"];
	str = [str stringByExpandingTildeInPath];
	
	[data writeToFile:str atomically:NO];



However, if I remove the comments, the JPEG is put through an NSImage and its quality is very poor and the JPEG is a much smaller size. I would very much appreciate any advice! Thanks

----

One hint unrelated to image processing: The following is silly and it will leak the allocated string

    
	str = [[NSString alloc] initWithString:@"~/Desktop/outImage.jpg"];
	str = [str stringByExpandingTildeInPath];


How about
    
	str = [@"~/Desktop/outImage.jpg" stringByExpandingTildeInPath];


----

Apologies, I am new to Cocoa and the concept of Wiki.

Essentially when using an NSImage I am not getting the quality that I need. Are there any resolution settings or quality options I can use to ensure that the image quality of my JPEG image is not lost when using NSImage?

----

If you can require Tiger, then I would suggest creating an NSGraphicsContext with your NSBitmapImageRep and drawing directly into that. The problem here is that your JPEG is at a resolution that's greater than screen resolution, and NSImage is throwing that away when you draw into it. Using an NSGraphicsContext directly will ensure that no data is thrown away. Note that you're recompressing the JPEG, so you'll get some quality loss no matter what you do.

----

Great, thanks for the help... 

But how can I get the data from the NSGraphicsContext back to my output file / NSBitmapImageRep? 

Much appreciated

*If you create an NSGraphicsContext from your NSBitmapImageRep, then it will use your image rep as its backing store, so any drawing you do will go directly into it.*

----

I thought that that might be the case, but it does not seem to be working (can you see any problems with the below code?) i.e. the NSData object data is the exact same as before, without the rectangle drawn over it.

    
NSBitmapImageRep* image = [[NSBitmapImageRep alloc] initWithData:[NSData dataWithContentsOfFile:str]];
	
	NSGraphicsContext* graphics = [[NSGraphicsContext graphicsContextWithBitmapImageRep:image] retain];

	[NSGraphicsContext saveGraphicsState];
	[NSGraphicsContext setCurrentContext:graphics];
	
	[[NSColor blackColor] set];
	[NSBezierPath fillRect:NSMakeRect(50,50,1000,1000)];

	[NSGraphicsContext restoreGraphicsState];
	
	NSDictionary *imageProps = [NSDictionary dictionaryWithObject:[NSNumber numberWithFloat:0.9] forKey:NSImageCompressionFactor];
	NSData* data = [image representationUsingType:NSJPEGFileType properties:imageProps];



Sorry for the persistent questions!

*I don't really understand what you are up to here, but such a "persistent" string of piecemeal questions with piecemeal answers is no substitute for understanding the corner of the framework you are using. I think you are really substituting conversation for careful reading. Such a Q-and-A can go on indefinitely. It was already clear from your initial post that reading the documentation in English was not your preferred mode. I am happy for you that there are more patient souls than I to assist you inch by inch. To those who are so patient in answering, I say, good for you for reading the documentation out loud to someone who wants to be treated like a child.*

----

I really have to wonder how much research you could possibly have as to the nature of your problem, when there was only 21 minutes between my response and your posting of code. When I took your code and tried it in a test program, there were some very obvious and blatant messages that got printed to the console, which led me to find the correct solution for this problem. Your don't mention it, so it either didn't occur for you (which I doubt, because if it didn't then your code would work), or you either saw it and decided not to mention it or simply didn't even look for it.

We are happy to help people with their problems, but it requires active participation on both sides. If you want somebody to do your work for you, then I'd be happy to send you my rate card.

Also, for future reference, "post to the cocoa-dev mailing list" is *not* a substitute for checking for errors, doing research, and providing all relevant information.

----
My answer to the top question...

You myImage object is specifying its size in user space resolution - which is generally 72dpi - whereas your original imageRep object is specified in actual pixel dimentions. If you have a 1600x1600 jpeg file, but it specifies 200dpi, an NSImage built from that will default to drawing much smaller than you want. You're specifying your size in two places - first, when you initialize the myImage object and again when you addRepresentation on it - so you may need the following code in two places or just one, I'm not sure:

    
NSSize mySize;
mySize.width = myImage bestRepresentationForDevice:nil] pixelsWide];
mySize.height = [[myImage bestRepresentationForDevice:nil] pixelsHigh];
[myImage setScalesWhenResized:YES];
[myImage setSize:mySize];


That should cause your output to be the same size (and at least close to the same quality) as your input.

Hope that helps,
[[BlakeSeely ( if that doesn't work, you can email me at my firstnamelastname @ mac.com )
----
Expanding on this a bit:  When you lockFocus on an image, the existing representations are destroyed and replaced with an NSCachedImageRep, which is more or less an offscreen window that you draw into.  There is a real live window backing your drawing, at screen resolution.  If your image had size 100x100 (even if the pixel count was 1200x1200), you now have only the 100x100 points to work with.
----

That's true - you can also turn off caching to prevent this, but you'll take a performance hit, and depending what you're doing with the image, may have to add a few other considerations to your code. I recommend leaving caching on and just being aware of what NSImage is doing with the reps... -BlakeSeely
----
Actually, you don't have much choice in this case.      -[NSImage lockFocus] will always replace your reps with an NSCachedImageRep, even if you've called     [image setDataRetained:YES].

Here's a useful tip though:  You can draw directly into an NSBitmapImageRep instead of locking focus on an image.  

**UNTESTED**
    
NSGraphicsContext *bitmapGraphicsContext = [NSGraphicsContext graphicsContextWithBitmapImageRep:bitmapImageRep];
[NSGraphicsContext saveGraphicsState];
[NSGraphicsContext setCurrentContext:bitmapGraphicsContext];

// standard drawing drawing commands go here

[NSGraphicsContext restoreGraphicsState];


Note that this only works on Tiger, and not all NSBitmapImageRep formats are supported. If your NSBitmapImageRep wasn't directly created by you (you got it from an NSImage, or you initialized it with a file) so that you don't control the format, you should create a second one with the appropriate format, then draw your original one into it to copy the data over. Otherwise you risk failure due to an unsupported bitmap format.

