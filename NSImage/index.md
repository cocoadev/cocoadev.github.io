---
layout: page
title: NSImage
---

http://developer.apple.com/library/mac/#documentation/Cocoa/Reference/ApplicationKit/Classes/NSImage_Class/
----

(Java only Question:)
"what does that boolean do in the constructor?
new NSImage(String String, boolean aBoolean)" 

this is irrelevant anyway as the constructor seems to be broken. It causes a JavaObjC FATAL exception whatever that is.

----

Does anyone have an implementation of an NSImage GetPixelRGBA function? I would like an easy way to simply get the RGBA value of a pixel somewhere in the NSImage without worrying about the representation.  (The A is optional)...Thanks!

----

Try this where theImage is an NSImage:

    
NSColor *theColor;
[theImage lockFocus];
theColor = NSReadPixel(NSMakePoint(x, y));
[theImage unlockFocus];


JoeZobkiw

------
this doesnt work for a one bit per pixel bitmap

----

I'm trying to append PDF data from an NSTextView to an NSImageView subclass (MyImageView, obtained from this site). 

    
NSTextView *textView;
MyImageView *myImg;
NSMutableData *myPDF = [myImg dataWithPDFInsideRect:[myImg bounds]];
[myPDF appendData:[textView dataWithPDFInsideRect:[textView bounds]]];


Thanks for the help! -- Hasan

----
Anybody have any ideas on why there is an appkit category called NSImage(NSTempHack), and what said category does?

*why do you care?*

Because I'm curious and I like to know how things work.

----
How do create a new NSImage instance from part of another image. I'm trying to capture several images from one. The rects for each segment are known, so I just need to know how to do it.
    
[newImage lockFocus];
[image1 compositeToPoint:locationInNewImage fromRect:rectToCopy operation:NSCompositeSourceOver];
[image2 compositeToPoint:locationInNewImage fromRect:rectToCopy operation:NSCompositeSourceOver];
...
[newImage unlockFocus];


I had seen compositeToPoint:fromRect:operation: in the docs, but I had totally forgotten about drawing into a new image. Thanks
----

I'm trying to pass an NSImage from one class to another after a function in one class has downloaded it from a site. It's in a screensaver, and nothing happens when the image should download, but when it tries to display the image, it quits. Here is how I declare the function:
    
- (NSImage *)homeURL:(NSString *)homeURL stringBeforeFile:(NSString *)stringBeforeFile stringAfterFile:(NSString *)stringAfterFile URLBeforeFile:(NSString *)URLBeforeFile;


And here is how I call the function:
-    
[getImage 
            homeURL:@"http://www.dilbert.com/index.html" 
            stringBeforeFile:@"<IMG SRC=\"/comics/dilbert/archive/images/" 
	     stringAfterFile:@"\" ALT=\"Today's Comic\""
	     URLBeforeFile:@"http://www.comics.com/comics/dilbert/archive/images/"];


(getImage is the object for the Get<nowiki/>Image class.)

Does anyone see anything obviously wrong with this? I know there are problems when passing arrays, but what about images?

*Design error: this sounds like a job for a class method, not an instance method.  You shouldn't have to instantiate an object to simply retrieve an image with all of the parameters you are passing.  Reconsider that design and implement it right and you'll have learned a good lesson about class vs. instance methods.*

----

What does "I know there are problems when passing arrays" mean? There are no problems when passing arrays between classes. The fact that you state this so surely leads me to believe that you actually have no idea what's going on....

Can you be more specific than just "it quits"? How does it quit, does it crash, what signal does it crash with, etc.?

----

It doesn't give a signal - it just ends the screensaver. It displays a few images, but when it gets to the one that uses this class, the screensaver ends.

*Does anything get logged to the Console? What happens when you use the debugger?*

The debugger doesn't work with screensavers, for some reason. But thanks for directing me to the Console. It gave me a long crash report, though i can't understand a lick of it. You can find it at http://xbean.gotdns.com/crash.txt .

And don't you have to do extra stuff when passing arrays between functions/classes? I might be totally wrong about this, but I thought I read somewhere that you had to declare it a constant and do some other things... Anyway, I can't exactly call myself experienced.

*No, this is wrong. Arrays are not special. Images aren't special either.*

Oh, I must be confusing it with some other stuff in C++. Thanks for the help. On second thought, it might be a problem with allocating/releasing the NSImage in the other class.

----

This looks like a very basic MemoryManagement problem with your image, probably you're using it later but forgetting to retain it.

----

I try to reduce the number of strings i render (in a flipped view) by storing them in an NSImage. However, the text is always drawn up-side-down whatever isFlipped or transforms i throw at it. I'm sure it is something trivial, Anyone?

    

    // check if we already have an image for this label
    NSImage *labelImage = [planet labelForKey:label];
    
    // nope, make one
    if (labelImage == nil) {
        // calculate size
        NSSize labelSize = [label sizeWithAttributes:normalStateAttribute];
        labelImage = [[[NSImage alloc] initWithSize:labelSize] autorelease];
        NSRect labelRect;
        labelRect.size = labelSize;
        labelRect.origin = NSZeroPoint;
        
        // setup the image
        [labelImage setCachedSeparately:YES]; // we go for speed vs mem
        [labelImage setScalesWhenResized: YES];
        [labelImage setDataRetained:YES];
        
        // the actual drawing      
        [labelImage lockFocus];
        [[NSColor whiteColor] set]; 
        [label drawInRect:labelRect withAttributes:normalStateAttribute];  
        [labelImage unlockFocus];        
        
        // and store it
        [planet setLabel:labelImage forKey:label];

    } 

    NSRect destRect;
    destRect.origin = planetViewBounds.origin;
    destRect.origin.y += planetViewBounds.size.height;
    int labelWidth =  [labelImage size].width;
    destRect.origin.x += (planetViewBounds.size.width - labelWidth) / 2;
    destRect.size = [labelImage size];
    
    NSRect sourceRect;
    sourceRect.origin = NSZeroPoint;
    sourceRect.size = [labelImage size];
    
    // actual draw
    [labelImage setFlipped:YES];
    [labelImage drawInRect:destRect fromRect:sourceRect operation:NSCompositeSourceOver fraction:1.0];



----
How do I represent a NSImage as NSData? I need to implement dataRepresentationOfType: for a new app I'm making and I need to save directly to an image format. - PietroGagliardi

----
Open NSImage.h, search for "NSData", and you will find what you're looking for.

----
Thanks, but that only provides me with a TIFF representation, and the     imageRepClassForFileType: doesn't return a class that takes TIFF data. I need step-by-step instructions to take TIFF data and convert it to any type of image data using a string value that contains the type. The documentation points me nowhere.

----
You should have said so at the beginning. You just said "an image format", and TIFF is certainly an image format.

You need to get an NSBitmapImageRep from your NSImage. The easiest way to do this is to use TIFFRepresentation on the NSImage, then make a new NSBitmapImageRep from that. NSBitmapImageRep can then create many different kinds of image formats.

----
OK. That solves 66% of my problem. The last 33% requires this: how do I take the ofType:(NSString *)aType parameter of dataRepresentation and give it to the NSBitmapImageRep? This would, theoretically, allow me to seamlessly save in any image format.

----
The types that NSBitmapImageRep can save as are near the top of NSBitmapImageRep.h. Create a table that maps types to those enums. For types that aren't in the list, they aren't supported, and you're out of luck unless you want to use an API with more flexibility such as QuickTime.

----
The enum really didn't help me (int values when I need a NSString * containing a literal string). However, upon further investigation, I found out that the aType variable is     @"Image"! I'm going back to the drawing board for a moment as I figure out this problem.

...

I wound up using     [self fileName] // (self is a NSDocument subclass to get the filename, extracting the extension, and mappingeach extension to their file types. I hope that I can find out how to implement the     bitmapImageRepForCachingDisplayInRect: and     cacheDisplayInRect:toBitmapImageRep: so they will let me do what     drawRect: does, but to an NSImage instead of to the screen. I'll get that done shortly.

----
I have the methods necessary.

1) Here is the code for     dataRepresentation:ofType: for your NSDocument subclass.
    
- (NSData *)dataRepresentationOfType:(NSString *)aType
{
	NSBitmapImageRep *rep;
	NSString *ext, *realName;
	char ext_temp;
	unsigned int ch;
	NSBitmapImageFileType ty;

	rep = [[NSBitmapImageRep alloc] initWithData:imageView result] [[TIFFRepresentation]];
	// begin routine to extract extension
	realName = [self fileName];
	for (ch = [realName length]; (ext_temp = [realName characterAtIndex:(ch - 1)]) != '.'; ch--)
		;
	ext = realName lowercaseString] substringFromIndex:ch];
	// end routine
	if ([ext isEqualToString:@"tif"] || [ext isEqualToString:@"tiff"])
		ty = [[NSTIFFFileType;
	else if ([ext isEqualToString:@"bmp"])
		ty = NSBMPFileType;
	else if ([ext isEqualToString:@"gif"])
		ty = NSGIFFileType;
	else if ([ext isEqualToString:@"jpg"] || [ext isEqualToString:@"jpeg"])
		ty = NSJPEGFileType;
	else if ([ext isEqualToString:@"png"])
		ty = NSPNGFileType;
	else // none of the above
		return nil;
	return [rep representationUsingType:ty properties:nil];
}


2) Add this method to your NSView subclass:
    
- (NSImage *)result
{
	NSData *tiffData;
	NSBitmapImageRep *rep;
	
	rep = [self bitmapImageRepForCachingDisplayInRect:[self frame]];
	[self cacheDisplayInRect:[self frame] toBitmapImageRep:rep];
	tiffData = [rep TIFFRepresentation];
	return [[NSImage alloc] initWithData:tiffData];
}


All this code by PietroGagliardi, 2007 Feb 20, released into public domain.

That should do it. - PietroGagliardi

UPDATE - My load... function had some unused variables in it. Removed. I also got rid of the NSLog() statements, used in debugging. All of these fixed on Feb 20, 2007, by PietroGagliardi

----
This worked for me
    
NSImage *newImage = [NSImageimageNamed:@"meagain.png"]; 
NSBitmapImageRep *bitmap = newImage representations] objectAtIndex:0];
[[NSData*data = [bitmap representationUsingType: NSJPEGFileTypeproperties: nil];


----
Question about memory usage:
When I have an NSImage with a source image of, say, 1024x768. Now I scale it down to 512x384. Does memory usage reflect that in anyway? Or is the full 1024x786 still in memory, it's just scaled down to 512x384? What would I have to do to make it use accordingly less memory? Draw the scaled image into a new image and release the original?
Thanks,

--MatthiasGansrigler

----
NSImage uses an NSImageRep to store the actual image data, and resizing the image won't change the amount of storage required by the NSImageRep. NSImage may or may not cache the image data, depending on the caching policy you set, and I believe that the size of the cache will decrease as you reduce the size of the NSImage. -CS
Pour vous joindre   garder le  numéro, vous aurez  peuvent avoir  compte  opérateur d'identification  ( Règle) [http://obtenir-rio.info numero rio]. Vous obtiendrez  pour  par appelant   mots  du serveur ou du service à la clientèle   votre actuel vieille fournisseur  [http://obtenir-rio.info/rio-bouygues code rio bouygues] . Vous ne  CAN   get un SMS avec votre . Avec  votre actuelle [http://obtenir-rio.info/rio-orange numero rio orange], alors  vous serez en mesure de vous abonner à l' offre de votre   en  rouge.

