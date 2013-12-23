---
layout: page
title: SavingNSImage
---

NSImage wont resize and save to disc correctly

I'm trying load an image into memory, resise it then save it back to disc. However I appear unable to resize the image as the copy I'm saving to disc ends up with the same dimensions as the original image. 

Can anyone help? I'm a newbie to Obj-C (not coding) so any advice on how I'm going about this is welcome. Current code below

    
NSSize newImageSize;
NSImage *myImage;
NSArray *representations;
NSData *bitmapData;

// assume fileToConvert holds filepath of first image and fileToConvert holds the new image path

// init image and get from disc
myImage = [[[NSImage alloc ] initWithContentsOfFile:fileToConvert] autorelease];
newImageSize.width = 75;
newImageSize.height = 75;
[myImage setSize:newImageSize];

// convert to bitmap and save
representations = [myImage representations];		
bitmapData = [NSBitmapImageRep representationOfImageRepsInArray:representations 
			 usingType:NSJPEGFileType properties:nil];
			 
[bitmapData writeToFile:fileToConvert atomically:YES];


----
Check out ThumbnailImages for resizing images sample code.

----
ThumbnailImages is a great example.  I want to stress that saving an NSImage via NSCoding will be wasteful (NSImage probably saves all its various representations and very possibly caches).  Be sure to extract the JPEG or TIFF data or whatever else and save that to disk.

----

You can also check out NSBitmapImageRepSetSizePreserveResolution where image resizing issues have been discussed recently (incl. some old sample code of mine) or you can also see this entry in my blog: http://web.mac.com/mabi99/iWeb/marcocoa/blog/5D09DF26-D533-409A-B72B-DBDE06AF748D.html for size considerations in NSImage / NSBitmapImageRep

--MarCocoa

----
Thanks guys, help most welcome and I can have certainly taken on-board the need to pull out only the representation needed before saving. 
However my understanding of why [myImage setSize:newImageSize] doesn't appear to work needs some help also. If I call the accessor method of myImage after changing the size, the new image size is returned and the apple documentation (link below) seems quite clear, at least to me.
http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/Classes/NSImage_Class/Reference/Reference.html#//apple_ref/occ/instm/NSImage/setSize:

AJ 


----
[image size] and [image setSize:] do not refer to the dimensions of the image in pixels, but in units (points) of the current display device (see my above mentioned blog-entry). In other words, using this method to downscale an NSBitmapImageRep or NSImage basically only enhances its resolution by keeping the pixel dimensions but drawing to a smaller area. If you want to actually downscale an image to yield less pixels, you need to interpolate the pixeldata. If you google for "resizing NSImage" you will probably find hundrets of discussions on that topic, many of which with complete solutions. But actually, the above mentioned sample projects should also give working solutions for resizing, don't they?!

Relating to your question about [myImage setSize:]: check out [image setScalesWhenResized:YES] and [myImage setCacheMode:NSImageCacheNever] in the developer docs...

--MarCocoa

