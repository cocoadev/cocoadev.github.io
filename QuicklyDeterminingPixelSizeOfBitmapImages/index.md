---
layout: page
title: QuicklyDeterminingPixelSizeOfBitmapImages
---



I'm wondering if there is a way to quickly find the exact size of an bitmap image file stored in arbitrary format.  Specifically I have large .tif, .png, and .jpg images on the disk, and I want to find the height and width.  Right now, my kloogy solution is to load in the image, determine the size, then throw away:

    
NSSize imageSize;
NSImageRep * imageRep = [NSImageRep imageRepWithContentsOfFile:imgFilePath];
imageSize.height = (float)[imageRep pixelsHigh];
imageSize.width = (float)[imageRep pixelsWide];


Unfortunatly this is very slow for huge images.  I've looked into incremental loading, but have had no luck.  I ran the following test code to see how much data I would have to load:

    
NSFileHandle * file = [NSFileHandle fileHandleForReadingAtPath:imgFilePath];
[file retain];
		
[file seekToFileOffset:0];
unsigned long long fileLength = [file seekToEndOfFile];
[file seekToFileOffset:0];
		
NSBitmapImageRep * imageRep = [[NSBitmapImageRep alloc] initForIncrementalLoad]; 
int retval = NSImageRepLoadStatusReadingHeader;
NSData * data;
unsigned long long posit = 0;
while( (retval==NSImageRepLoadStatusReadingHeader) && ((posit+1000) < (fileLength-1)) )
{	
  NSAutoreleasePool* loopPool = [[NSAutoreleasePool alloc] init];
  data = [file readDataOfLength:1000];
  retval = [imageRep incrementalLoadFromData:data complete:NO];
  posit = [file offsetInFile];
  [loopPool release];
}	
if (retval==NSImageRepLoadStatusReadingHeader)	
{
  data = [file readDataToEndOfFile];
  retval = [imageRep incrementalLoadFromData:data complete:YES];
  posit = [file offsetInFile];
}

NSLog(@"File length: %qu, amount read: %qu",fileLength,posit);
NSSize imageSize;
imageSize.height = (float)[imageRep pixelsHigh];
imageSize.width = (float)[imageRep pixelsWide];
NSLog(@"H %f W %f",imageSize.height,imageSize.width);


But most the time, even after loading all the data, it still can not determine the size of the image. I've tried this with TIFF, BMP, and JPEG files. Am I doing something wrong? I haven't used incremental loading before.

I was under the impression that most standard formats have some kind of header at the beginning where information such as height and width are stored.  It seems there must be someway to quickly read this, because in Finder if I "Get Info" on one of these files, all the specs, including dimensions, instantly pops up.

Thanks,
Ken

----
Don't forget that the info from the Finder is probably indexed already by Spotlight.

----
The Finder actually does not rely on Spotlight for this information. The feature has been in the Finder since before Spotlight existed (not in the Get Info window, but via the "Show Item Info" option of icon view), and even in Tiger it works fine on non-indexed volumes. I would love to be able to access Apple's routine for this, since it's fast and works with a wide variety of files, but I don't know how. Maybe there's some way to do it with QuickTime?

----
It seems you're exactly right.  I'm still kind of curious if there is a way to get it directly from the file, but accessing the spotlight metadata certainly solves the problem. Following the notes at http://developer.apple.com/macosx/spotlight.html, I have something that seems to work:
    
unsigned long long height,width; 
MDItemRef item = MDItemCreate(kCFAllocatorDefault, (CFStringRef)filePath);
CFNumberRef ref = MDItemCopyAttribute(item, kMDItemPixelHeight);
Boolean checkH = CFNumberGetValue( ref, CFNumberGetType(ref), &height);
ref = MDItemCopyAttribute(item, kMDItemPixelWidth);
Boolean checkW = CFNumberGetValue( ref, CFNumberGetType(ref), &width);
NSLog(@"H %qu W %qu",height,width);

much much quicker this way.  Thanks for pointing me in the right direction!

----

Problem with that is if the image comes from any source not indexed by spotlight.
A possible better way, and one that I have used is to use Quicktime.  -- arau

This is the code for finding image info from an NSData instance.
    
    GraphicsImportComponent quickTimeImporter = 0;
    ImageDescriptionHandle imageParametersHandle = 0;
    ComponentInstance dataHandler;
    PointerDataRef dataref = (PointerDataRef)NewHandle(sizeof(PointerDataRefRecord));
    (** dataref).data = (void *)[imageData bytes];
    (** dataref).dataLength = [imageData length];
    OpenADataHandler( (Handle)dataref, PointerDataHandlerSubType, NULL, (OSType)0, NULL, kDataHCanRead, &dataHandler);
    GetGraphicsImporterForDataRef( (Handle)dataref, PointerDataHandlerSubType, &quickTimeImporter);
    GraphicsImportGetImageDescription(quickTimeImporter, &imageParametersHandle);
    DisposeHandle((Handle)dataref);
    NSSize imageSize = NSMakeSize((*imageParametersHandle)->width , (*imageParametersHandle)->height); 
    CloseComponent(quickTimeImporter);
    DisposeHandle((Handle)imageParametersHandle);


Or slightly simpler code by giving the graphics importer a url.

    
    FSSpec imageLocation;
    FSRef imageFileRef;
    CFURLRef sourceURLRef = (CFURLRef)[NSURL fileURLWithPath: anImagePath];
    CFURLGetFSRef( sourceURLRef, &imageFileRef);
    FSGetCatalogInfo(&imageFileRef, kFSCatInfoNone, nil, nil, &imageLocation, nil);
    GetGraphicsImporterForFile(&imageLocation, &quickTimeImporter);
    GraphicsImportGetImageDescription(quickTimeImporter, &imageParametersHandle);
    NSSize imageSize = NSMakeSize((*imageParametersHandle)->width , (*imageParametersHandle)->height); 
    CloseComponent(quickTimeImporter);
    DisposeHandle((Handle)imageParametersHandle);


----

Don't forget to release objects you "own". --zootbobbalu

    

	SInt32 width = 0, height = 0; 
	MDItemRef item = MDItemCreate(kCFAllocatorDefault, CFSTR("/Library/Desktop Pictures/Aqua Blue.jpg"));
	Boolean hasDimensions = FALSE;

	if (item) {
		CFNumberRef heightValue = MDItemCopyAttribute(item, kMDItemPixelHeight);
		if (heightValue) {
			if (CFNumberGetValue(heightValue, kCFNumberSInt32Type, &height)) {
				CFNumberRef widthValue = MDItemCopyAttribute(item, kMDItemPixelWidth);
				if (widthValue) {
					hasDimensions = CFNumberGetValue(widthValue, kCFNumberSInt32Type, &width);
					CFRelease(widthValue);
				}
			}
			CFRelease(heightValue);
		}
		CFRelease(item);
	}
	
	NSLog(@"hasDimensions: %@ wh { %i, %i }", (hasDimensions) ? @"YES" : @"NO", width, height);


----
Oh, thanks for the heads up...I haven't used any CF stuff before.  It looks like memory managment is similar to the familiar reference count of NSObjects; I guess I should look through the documentation.

----
CF refcounting is basically identical to Cocoa refcounting. The differences are that CF has no concept of autorelease, so you have to manually release a lot more things, and     CFRelease(NULL) is an error but     [nil release] is not.

