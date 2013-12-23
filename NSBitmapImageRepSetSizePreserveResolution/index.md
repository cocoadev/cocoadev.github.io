---
layout: page
title: NSBitmapImageRepSetSizePreserveResolution
---

I am working on an Image captioning program and have been asked to add a feature that resize all images in the open folder to 10" a side. The images are of variable resolution and never actually need to be drawn to a view (not in their full form at least). 

The current method I have "works" to resize them, but not really. So far I've gotten it to either resize it to 10 inches but change the resolution at the same time, or just change the resolution while keeping the same pixel height and width as the original image.

I've done a fair amount of browsing this site and many others, and made lots of modifications to this function, so forgive me if it's a little messy, I plan on cleaning it up once I get it working.

I've tried using NSImage and NSBitmapImageRep, and get similar results with each one.

Is there a way to resize an image without having it get converted to 72 DPI. I want to leave that at the value of the image and just change the pixels height and width, without mucking everything else up.
    
- (IBAction)resizeAllImages:(id)sender
{
	int i;
	int fcnt = [fileNameList count]; //Number of files currently open
	NSString * currentFile;
	NSBitmapImageRep * theImageRep;
	NSSize imageSize;
	float wRes, hRes, widthInches, heightInches, newWidth, newHeight, pixelsWide, pixelsHigh;

	NSData *bitmapData;
	NSDictionary *imageProps = [NSDictionary dictionaryWithObject:[NSNumber numberWithFloat:0.8] forKey:NSImageCompressionFactor];
	
    for(i=0; i < fcnt ; i++) 
    {
		currentFile = fileNameList objectAtIndex:i] fileName]; //Current file to be worked with (resized if needed)		theImageRep = [[[NSBitmapImageRep imageRepWithContentsOfFile:currentFile];
		imageSize = [theImageRep size];
		pixelsWide = (float)[theImageRep pixelsWide];
		pixelsHigh = (float)[theImageRep pixelsHigh];
		wRes = 72 * pixelsWide / imageSize.width;
		hRes = 72 * pixelsHigh / imageSize.height;
		widthInches = pixelsWide / wRes;
		heightInches = pixelsHigh / hRes;
		if( (widthInches <= 10.0) && (heightInches <= 10.0) )
		{
			NSLog(@"%i: skipped: w:%f,h:%f", i,widthInches,heightInches);
		}
		else 
		{
			if (widthInches > heightInches)
			{
				newHeight = heightInches * (10.0/widthInches) * wRes;
				newWidth = 10 * wRes;
			}
			else if (heightInches > widthInches)
			{
				newWidth = widthInches * (10.0/heightInches) * hRes;
				newHeight = 10 * hRes;
			}
			else
			{
				newHeight = 10 * hRes;
				newWidth = 10 * wRes;
			}
			imageSize.width=newWidth;
			imageSize.height=newHeight;
			
			[theImageRep setSize:imageSize];
			
			bitmapData = [theImageRep representationUsingType:NSJPEGFileType properties:imageProps];
			[bitmapData writeToFile:currentFile atomically:NO];
			[self writeIPTCDataWithFileName:currentFile withIndex:i]; //Write the IPTC data back to file after we obviously deleted it in the resize.
		}
    }
		
}


----

I am not exactly sure, what you intend to do. Maybe I got you wrong, but if you plan to change the actual "inch-size" of any given image without altering the pixels, you naturally end up with an image of a different resolution (resolution as in dpi)- e.g. with a 20x30 inch image at 300 dpi you d go down to 10x15 inch at 600 dpi. If you want to maintain the original resolution (in this example 300 dpi), you need to re-calculate the image as you will decrease its actual pixel number. There is no such functionality to NSBitmapImageRep (as far as I know). However, NSImage does scale images, look into the NSImage method setScalesWhenResized:. You could then create a new NSBitmapImageRep by initWithFocusedViewRect: I think to recall.

One more thing- it's been a while since I last played with NSBitmapImageRep, but I think there (could have been Jaguar or so, not sure) was a problem I had with NSJPEGRepresentation in that it never reflected the resolution that I gave it (i.e. the actual pixels were correct, just the res was always 72 dpi), not so for TIFFs, they worked fine. I never looked into that, not even checked whether the JPEG format per se even can specify a resolution or not.

Maybe I should add, that I could be completely wrong in all I said, as I did not look up anything of it to make sure...


--marcocoa


----
I guess what I mean to do, more simply put is: Read in a JPEG file. Resize it to 10 inches on the longest side, resave it, preserving resolution, save it back as a JPEG. So if it is say 1728x1295 pixels @ 144 DPI. 12" x 8.993", so then to make it 10" on the longest side I'd want it to change it to: 1440x1079 pixels @ 144DPI, 10" x 7.494". How do I do this with Cocoa? It seems like such an easy concept, and it's got me baffled.

----
As I said, even if the concept is easy, the actual work is NOT, as all pixels need to be recalculated based on any one of the interpolation algorithms out there. However, you can (ab)use NSImage to do the re-scaling, extract the imageRep and there you go. A coupe of years back, when I still had time and was kind of active on the cocoadev
mailinglist, I put up some sample code to demonstrate working with NSBitmapImageReps because some ppl on the list had similar trouble as you. You can access my posting with the links to the sample projects (still valid) at: http://www.omnigroup.com/mailman/archive/macosx-dev/2003-June/046807.html

Another, more modern and flexible way of dealing with all kinds of image manipulatins is using Core Image. For a very helpful sample, in which they use a CIFilter to scale an image (CILanczosScaleTransform), see Apple's "Reducer" project that you can find at: http://developer.apple.com/samplecode/Reducer/ 

Finally, so that people don't say I'm not helpful to other readers of this site as I don't give concrete solutions, here is a snippet of code from one of my mentioned sample projects. The code comes from a category on NSImageRep, so "self" refers to the imageRep that you want to scale:

    
        if ([self isKindOfClass:[NSBitmapImageRep class]]) {
            NSImage *tempImage = [[NSImage alloc] init];

            [tempImage addRepresentation:self];
            auxImage = [[NSImage alloc] initWithSize: NSMakeSize(_pixelsWide*resolutionFactor,_pixelsHigh*resolutionFactor)];

            [auxImage lockFocus]; 

            [tempImage drawInRect:NSMakeRect(0,0,_pixelsWide*resolutionFactor,_pixelsHigh*resolutionFactor) fromRect:NSZeroRect operation:NSCompositeSourceOver fraction:1.0];

            [auxImage unlockFocus];
            [tempImage release];
            
            NSBitmapImageRep* scaledImageRep = [[NSBitmapImageRep alloc] initWithData:[auxImage TIFFRepresentation]];
        }


This is one way to do it, there are more of course. I hope this was of some help...

--marcocoa
----
I'll go thru that now and let you know what I come up with. I forgot to post it before, but you've been very helpful. Thanks =)

Your code gets me 1 step closer, and actually seems to have the same result as Reducer. Reducer saves jpegs at 72 DPI. This is an odd thing as other image manipulation programs are able to use different resolutions with jpegs (Photoshop for example). More as I do more research. 

As it stands now, it is resizing the image "correctly" just at 72 DPI, instead of 144 DPI as the original jpeg was.

-Morphie

----

As I said, I faintly remembered something like this but I never got into this... Sorry, from here on, I cant help you any further! Mab be the Apple code is flawed in that respect. You should go ask this in the cocoadev mailing list at Apple, there also actual Apple engineers might respond!

--marcocoa

----
You have gotten me further, and also helped me to know that I'm not being totally stupid! This is more valuable than you might realize. Seeing Reducer and having it have the same issues, is frustrating, but encouraging (in some strange way). I'll post here when I get a solution, or to respond if more comes along. Once again, thanks for your help so far.

-Morphie

