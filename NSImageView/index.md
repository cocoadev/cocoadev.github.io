---
layout: page
title: NSImageView
---

NSImageView

An NSImageView displays a single NSImage in a frame and can optionally allow a user to drag an image to it. http://goo.gl/OeSCu

----

To display an image from the application bundle using an NSImageView use the following code:

    
// Header
IBOutlet *NSImageView imageView;

// Source
NSImage *imageFromBundle = [NSImage imageNamed:@"your_image.tif"];

[imageView setImage: imageFromBundle];


----

To download and display an image from a server using an NSImageView use the following code:

    
// Header
IBOutlet *NSImageView imageView;

// Source
NSURL *imageURL = [NSURL URLWithString:@"http://www.someserver.com/image.tif"];
NSData *imageData = [imageURL resourceDataUsingCache:NO];
NSImage *imageFromBundle = [[NSImage alloc] initWithData:imageData];

if (imageFromBundle)
{
    // The image loaded properly, so lets display it.
    [imageView setImage:imageFromBundle];
    [imageFromBundle release];
}
else  //  if (!imageFromBundle)
{
    // The image did not load properly, so lets send an error to the log.
    // At this time, we could either load a copy of the image from the bundle,
    // or simply display an error sheet.
    NSLog(@"imageView could not be loaded.");
}


----

Other failure modes:  URL, Data?

----

The code is just meant to be a simple examples showing how to do a few things with NSImageView.

----

**FakeImageView**

Despite my efforts the following statement refuses to live up to itself:

* NSScaleProportionally. If the image is too large, it shrinks to fit inside the frame. If the image is too small, it expands. The proportions of the image are preserved.

It shrinks to fit inside the frame, but it does NOT expand to fill the frame.

----

This is the intended, if poorly documented, behavior of NSImageView. It's worked that way in OpenStep as well. Feelings of mistrust or betrayal are natural.

I ended up writing my own class, which I call FakeImageView, to implement this and a few other features I wanted. Click through for more details.

When necessary, I create a FakeImageView subclass of NSView in IB and set image views to that custom class. Maybe it would have been better to subclass NSImageView ... but oh well. Your mileage may vary. 

-- MikeTrent

----

I liked the Drag and Drop support of NSImageView -- I'm going to try to add that functionality to your FakeImageView.

----

Is there a way to repeat an image over the X axis, forming a pattern?

----

Sure. If I was doing it, I'd add that to NSImageCategory though.     +patternImage: alongAxis: size:, maybe.

----

See NSColor's colorWithPatternImage: method.

----

I needed an image view to use in a CoreData project that would let me get and set the path of the displayed image (because I wanted to use discrete image files instead of ballooning the CD store). Here's what I came up with.

    
//// Header
#import <Cocoa/Cocoa.h>

@interface PRImageView : NSImageView
{
	AliasHandle mImageAlias;
	BOOL mDraggingFlag;
}

- (NSString*)imagePath;
- (NSURL*)imageURL;
- (AliasHandle)imageAlias;
- (BOOL)getImageRef:(FSRef*)outRef;

- (void)setImageFromPath:(NSString*)inPath;
- (void)setImageFromURL:(NSURL*)inURL;

@end

//// Implementation
//
//  PRImageView.m
//
//  Created by Gregory Weston on 3/5/08.
//

#import "PRImageView.h"


@implementation PRImageView

- (void)cacheAliasFromURL:(NSURL*)inURL
{
	FSRef theRef = {};
	CFURLGetFSRef((CFURLRef)inURL, &theRef);
	OSErr theError = FSNewAlias(NULL, &theRef, &mImageAlias);
	if(theError != noErr) NSLog(@"FSNewAlias: %d", theError);
}

- (void)clearCachedAlias
{
	if(mImageAlias)
	{
		DisposeHandle((Handle)mImageAlias);
		mImageAlias = NULL;
	}
}

- (void)dealloc
{
	DisposeHandle((Handle)mImageAlias);
	[super dealloc];
}

- (void)setImage:(NSImage*)inImage
{
	if(mDraggingFlag == NO) [self clearCachedAlias];
	[super setImage:inImage];
}

- (void)concludeDragOperation:(id <NSDraggingInfo>)sender
{
	[self clearCachedAlias];

	NSPasteboard* thePasteboard = [sender draggingPasteboard];
	NSArray* theTypes = [thePasteboard types];
	if([theTypes containsObject:NSFilenamesPboardType])
	{
		NSArray* thePaths = [thePasteboard propertyListForType:NSFilenamesPboardType];
		NSString* thePath = [thePaths objectAtIndex:0];
		NSURL* theURL = [NSURL fileURLWithPath:thePath];
		[self cacheAliasFromURL:theURL];
	}

	mDraggingFlag = YES;
	[super concludeDragOperation:sender];
	mDraggingFlag = NO;
}

- (NSString*)imagePath
{
	NSString* theResult = nil;
	NSURL* theURL = [self imageURL];
	if(theURL && [theURL isFileURL])
	{
		theResult = [theURL path];
	}
	return theResult;
}

- (NSURL*)imageURL
{
	NSURL* theResult = nil;
	FSRef theRef = {};
	if([self getImageRef:&theRef])
	{
		CFURLRef theURL = CFURLCreateFromFSRef(kCFAllocatorDefault, &theRef);
		if(theURL)
		{
			theResult = [(NSURL*)theURL autorelease];
		}
	}
	return theResult;
}

- (AliasHandle)imageAlias
{
	return mImageAlias;
}

- (BOOL)getImageRef:(FSRef*)outRef
{
	BOOL theResult = NO;
	if(mImageAlias && outRef)
	{
		Boolean theChangeFlag = false;
		if(FSResolveAlias(NULL, mImageAlias, outRef, &theChangeFlag) == noErr)
		{
			theResult = YES;
		}
	}
	return theResult;
}

- (void)setImageFromPath:(NSString*)inPath
{
	if(inPath)
	{
		NSImage* theImage = [[NSImage alloc] initWithContentsOfFile:inPath];
		if(theImage)
		{
			[self setImage:theImage];
			[self clearCachedAlias];
			NSURL* theURL = [NSURL fileURLWithPath:inPath];
			[self cacheAliasFromURL:theURL];
		}
	}
}

- (void)setImageFromURL:(NSURL*)inURL
{
	if(inURL)
	{
		NSImage* theImage = [[NSImage alloc] initWithContentsOfURL:inURL];
		if(theImage)
		{
			[self setImage:theImage];
			[self clearCachedAlias];
			if([inURL isFileURL]) [self cacheAliasFromURL:inURL];
		}
	}
}

@end


