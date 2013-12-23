---
layout: page
title: HowToConvertCIImageToNSData
---

Can any body know how to get NSData from CIImage?


----
First, you'll have to convert CIImage to NSImage, like so:
    
NSImage *image = [[[NSImage alloc] initWithSize:[ciImage extent].size]
	autorelease];
[image lockFocus];
CGContextRef contextRef =
	[[NSGraphicsContext currentContext]
	graphicsPort];
CIContext *ciContext =
	[CIContext contextWithCGContext:contextRef
	options:[NSDictionary dictionaryWithObject:
	[NSNumber numberWithBool:YES]
	forKey:kCIContextUseSoftwareRenderer]];
[ciContext drawImage:ciImage
	atPoint:CGPointMake(0, 0) fromRect:[ciImage extent]];
[image unlockFocus];

Then you'll get an NSData object when using [image TIFFRepresentation].
Of course, this is just one of thousands of possibilities.

Take care,
--MatthiasGansrigler

----
Yes it works. But I found that the step [image TIFFRepresentation] to get tiff data is slow. Is there other way to get image data?.

----
When someone comes up with a valid answer to your question but that answer does not work for you, that means that you have insufficiently specified your question.

Be more specific than "get image data". If speed is a requirement, state it. Tell us what *kind* of image data you want, and what you need to do with it. Then maybe we can give you an answer that you want.

----
Additionally, read MailingListMode and consider taking this to an actual mailing list. This is not a basic Q&A site, it's a wiki.
----
You could play around with NSImageReps, maybe that helps somewhere... Here is an updated way to convert a CIImage to an NSImage. (here: http://www.eternalstorms.at/developers/page39/page39.html you can get an NSImage (and CIImage) category that does all this and more for you. If advertisement, please remove and my apologies).

    
CIImage *myCIImage;
NSImage *img = [[NSImage alloc] initWithSize:[myCIImage extent].size];
NSCIImageRep *rep = [NSCIImageRep imageRepWithCIImage:myCIImage];
[img addRepresentation:rep];

This is less code and probably a little faster.

Take care,

--MatthiasGansrigler

