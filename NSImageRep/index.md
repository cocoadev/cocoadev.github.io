---
layout: page
title: NSImageRep
---

NSImageRep is a concrete image representation for use by NSImage or directly.

Among other things, it can be subclassed to easily make NSImage support custom image types.
The online documentation is a bit inaccurate. Here's an example of how you can implement your own NSImageRep:

    
@interface CustomImageRep : NSImageRep {
	CGImageRef image;
}
@end

@implementation CustomImageRep
+ (NSArray *)imageUnfilteredTypes {
	// This is a UTI
	return [NSArray arrayWithObjects:
		@"my.custom.imagetype",
		nil
	];
}
+ (NSArray *)imageUnfilteredFileTypes {
	// This is a filename suffix
	return [NSArray arrayWithObjects:
		@"cimg",
		nil
	];
}
+ (BOOL)canInitWithData:(NSData *)data {
	if ([data length] >= sizeof(CustomImageHeader)) {
		const char *magic = [data bytes];
		if (memcmp(kCustomImageMagic, magic, sizeof(kCustomImageMagic)) == 0) {
			return YES;
		}
	}
	return NO;
}
+ (id)imageRepWithData:(NSData *)data {
	return [self alloc] initWithData:data] autorelease];
}
+ (id)imageRepWithContentsOfFile:([[NSString *)filename {
	NSData *data = [NSData dataWithContentsOfFile:filename];
	return [self alloc] initWithData:data] autorelease];
}
+ (id)imageRepWithContentsOfURL:(NSURL *)aURL {
	[[NSData *data = [NSData dataWithContentsOfURL:aURL];
	return [self alloc] initWithData:data] autorelease];
}
- (id)initWithData:([[NSData *)data {
	self = [super init];
	if (!self) {
		return nil;
	}
	
	// Load your image here
	
	int width = CGImageGetWidth(image);
	int height = CGImageGetHeight(image);
	if (width <= 0 || height <= 0) {
		NSLog(@"Invalid image size: Both width and height must be > 0");
		[self autorelease];
		return nil;
	}
	[self setPixelsWide:width];
	[self setPixelsHigh:height];
	[self setSize:NSMakeSize(width, height)];
	[self setColorSpaceName:NSDeviceRGBColorSpace];
	[self setBitsPerSample:8];
	[self setAlpha:YES];
	[self setOpaque:NO];
	
	return self;
}
- (void)dealloc {
	CGImageRelease(image);
	[super dealloc];
}
- (BOOL)draw {
	CGContextRef context = [[NSGraphicsContext currentContext] graphicsPort];
	if (!context || !image) {
		return NO;
	}
	NSSize size = [self size];
	CGContextDrawImage(context, CGRectMake(0, 0, size.width, size.height), image);
	return YES;
}
@end


To make it work automatically when calling     [[NSImage alloc] initWith*], you have to register it.
Add this to some class that gets loaded early:
    
+ (void)initialize {
	[NSImageRep registerImageRepClass:[CustomImageRep class]];
}


NSBitmapImageRep might work too. But I had some trouble with it. NSImageRep is easy enough to implement and works like a charm.

