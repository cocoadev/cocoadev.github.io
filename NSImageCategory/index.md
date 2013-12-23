---
layout: page
title: NSImageCategory
---




NSImage+CCDAdditions.h

 @interface NSImage (CocoaDevUsersAdditions)
 + (NSImage *)scaleImage:(NSImage *)image toSize:(NSSize)newSize proportionally:(BOOL)prop;
 @end


NSImage+CCDAdditions.m

 @implementation NSImage (CocoaDevUsersAdditions)
 
 + (NSImage *)scaleImage:(NSImage *)image toSize:(NSSize)newSize proportionally:(BOOL)prop
 {
     if (image) {
         NSImage *copy = [image copy];
 		NSSize size = [copy size];
 
 		if (prop) {
 			float rx, ry, r;
 
                 rx = newSize.width / size.width;
                 ry = newSize.height / size.height;
                 r = rx < ry ? rx : ry;
                 size.width *= r;
                 size.height *= r;
 		} else
 			size = newSize;
 	
 	   [copy setScalesWhenResized:YES];
 	   [copy setSize:size];
 
         return [copy autorelease];
     }
    return nil; // or 'image' if you prefer.
 }
 
 @end





4/2; Added proportional scaling, borrowing from FakeImageView.

----

A category to make it easy to draw an image scaled and centered in a given NSRect.



NSImage_CenteredDrawingAdditions.h

 @interface NSImage (CenteredDrawing)
 // draws the passed image into the passed rect, centered and scaled appropriately.
 // note that this method doesn't know anything about the current focus, so the focus must be locked outside this method
 - (void)drawCenteredinRect:(NSRect)inRect operation:(NSCompositingOperation)op fraction:(float)delta;
 @end


NSImage_CenteredDrawingAdditions.m

 @implementation NSImage (CenteredDrawing)
 
 // draws the passed image into the passed rect, centered and scaled appropriately.
 // note that this method doesn't know anything about the current focus, so the focus must be locked outside this method
 - (void)drawCenteredinRect:(NSRect)inRect operation:(NSCompositingOperation)op fraction:(float)delta
 {
 		NSRect srcRect = NSZeroRect;
 		srcRect.size = [self size];
 
 		// create a destination rect scaled to fit inside the frame
 		NSRect drawnRect = srcRect;
 		if (drawnRect.size.width > inRect.size.width)
 		{
 			drawnRect.size.height *= inRect.size.width/drawnRect.size.width;
 			drawnRect.size.width = inRect.size.width;
 		}
 
 		if (drawnRect.size.height > inRect.size.height)
 		{
 			drawnRect.size.width *= inRect.size.height/drawnRect.size.height;
 			drawnRect.size.height = inRect.size.height;
 		}
 
 		drawnRect.origin = inRect.origin;
 
 		// center it in the frame
 		drawnRect.origin.x += (inRect.size.width - drawnRect.size.width)/2;
 		drawnRect.origin.y += (inRect.size.height - drawnRect.size.height)/2;
 
 		[self drawInRect:drawnRect fromRect:srcRect operation:op fraction:delta];
 }
 
 @end



----

A category to turn drawing an image or bitmap image representation with a CoreImage filter into (almost) a one-liner. See http://developer.apple.com/documentation/GraphicsImaging/Conceptual/CoreImaging/ci_filters/chapter_5_section_1.html for a list of default Core Image filters and their arguments.

To use this code, you need to add the Core Image framework to your Xcode project. The framework is located at /System/Library/Frameworks/QuartzCore.framework.

This was written using the whitespace and bracket style I personally prefer. It's not the normal Objective-C style. My apologies--I hope you still find it useful. - JonathanGrynspan

Example usage:

 NSImage *anImage;
 NSRect bounds;
 
 anImage = [NSImage imageNamed: @"A Bundled Image"];
 if (anImage) {
 	bounds.origin = NSZeroPoint;
 	bounds.size = [anImage size];
 
 	[anImage drawAtPoint: NSZeroPoint fromRect: bounds coreImageFilter: @"CIColorInvert" arguments: nil];
 }



NSImage_CoreImageAdditions.h

 #import <Cocoa/Cocoa.h>
 
 @interface NSImage (CoreImage)
 /* Draws the specified image using Core Image. */
 - (void)drawAtPoint: (NSPoint)point fromRect: (NSRect)fromRect coreImageFilter: (NSString *)filterName arguments: (NSDictionary *)arguments;
 
 /* Gets a bitmap representation of the image, or creates one if the image does not have any. */
 - (NSBitmapImageRep *)bitmapImageRepresentation;
 @end




NSImage_CoreImageAdditions.m

 #import "NSImage_CoreImageAdditions.h"
 
 @implementation NSImage (CoreImage)
 - (void)drawAtPoint: (NSPoint)point fromRect: (NSRect)fromRect coreImageFilter: (NSString *)filterName arguments: (NSDictionary *)arguments {
 	NSAutoreleasePool *pool;
 	NSBitmapImageRep *rep;
 		
 	pool = NSAutoreleasePool alloc] init];
 	
 	if (filterName) {
 		rep = [self bitmapImageRepresentation];
 		[rep
 			drawAtPoint:		point
 			fromRect:		fromRect
 			coreImageFilter:	filterName
 			arguments:		arguments];
 	} else {
 		/* bypass core image if no filter is specified */
 		[self
 			drawAtPoint:		point
 			fromRect:		fromRect
 			operation:		NSCompositeSourceOver
 			fraction:		1.0f];
 	}
 
 	[pool release];
 }
 
 - (NSBitmapImageRep *)bitmapImageRepresentation {
 	NSImageRep *rep;
 	NSEnumerator *e;
 	Class bitmapImageRep;
 
 	bitmapImageRep = [NSBitmapImageRep class];
 	e = [[self representations] objectEnumerator];
 	while ((rep = [e nextObject]) != nil) {
 		if ([rep isKindOfClass: bitmapImageRep])
 			break;
 		rep = nil;
 	}
 	
 	if (!rep)
 		rep = [NSBitmapImageRep imageRepWithData: [self TIFFRepresentation;
 	
 	return (NSBitmapImageRep *)rep;
 }
 
 @end




NSBitmapImageRep_CoreImageAdditions.h

 #import <Cocoa/Cocoa.h>
 
 #define CIIMAGE_PADDING 16.0f
 
 @interface NSBitmapImageRep (CoreImage)
 /* Draws the specified image representation using Core Image. */
 - (void)drawAtPoint: (NSPoint)point fromRect: (NSRect)fromRect coreImageFilter: (NSString *)filterName arguments: (NSDictionary *)arguments;
 @end




NSBitmapImageRep_CoreImageAdditions.m

 #import "NSBitmapImageRep_CoreImageAdditions.h"
 #import <QuartzCore/QuartzCore.h>
 
 @implementation NSBitmapImageRep (CoreImage)
 - (void)drawAtPoint: (NSPoint)point fromRect: (NSRect)fromRect coreImageFilter: (NSString *)filterName arguments: (NSDictionary *)arguments {
 	NSAutoreleasePool *pool;
 	CIFilter *filter;
 	CIImage *before;
 	CIImage *after;
 	CIContext *ciContext;
 	CGContextRef cgContext;
 	
 	pool = [[NSAutoreleasePool alloc] init];
 	before = nil;
 	
 	@try {
 		before = [[CIImage alloc] initWithBitmapImageRep: self];
 		if (before) {
 			filter = [CIFilter filterWithName: filterName];
 			[filter setDefaults];
 			if (arguments)
 				[filter setValuesForKeysWithDictionary: arguments];
 			[filter setValue: before forKey: @"inputImage"];
 		} else {
 			filter = nil;
 		}
 		
 		after = [filter valueForKey: @"outputImage"];		
 		if (after) {
 			if (![[arguments objectForKey: @"gt_noRenderPadding"] boolValue]) {
 				/* Add a wide berth to the bounds -- the padding can be turned
 				   off by passing an NSNumber with a YES value in the argument
 				   "gt_noRenderPadding" in the argument dictionary. */
 				fromRect.origin.x -= CIIMAGE_PADDING;
 				fromRect.origin.y -= CIIMAGE_PADDING;
 				fromRect.size.width += CIIMAGE_PADDING * 2.0f;
 				fromRect.size.height += CIIMAGE_PADDING * 2.0f;
 				point.x -= CIIMAGE_PADDING;
 				point.y -= CIIMAGE_PADDING;
 			}
 
 			cgContext = CGContextRetain((CGContextRef)[[NSGraphicsContext currentContext] graphicsPort]);
 			if (cgContext) {
 				ciContext = [CIContext contextWithCGContext: cgContext options: nil];
 				[ciContext
 					drawImage:		after
 					atPoint:		*(CGPoint *)(&point)
 					fromRect:		*(CGRect *)(&fromRect)];
 				CGContextRelease(cgContext);
 			}
 		}
 	} @catch (NSException *e) {
 		NSLog("exception encountered during core image filtering: %@", e);
 	} @finally {
 		[before release];
 	}
 	
 	[pool release];
 }
 @end


(March 23, 2006: edited the above code to fix some minor bugs. --JonathanGrynspan)
(March 16, 2007: added call to -setDefaults on CIFilter in case there's no arguments dictionary. Very bad things happen with some filters otherwise. --GrahamCox

----

A category for getting an NSImage from an NSImageRep in one line of code. Especially useful when you need to draw the image rep, taking into consideration the image rep's alpha layer. -Jonathan Grynspan

NSImage+ImageRepToImage.h
 @interface NSImage (ImageRepToImage)
 + (NSImage *)imageWithImageRep: (NSImageRep *)rep;
 @end

NSImage+ImageRepToImage.m
 @implementation NSImage (ImageRepToImage)
 + (NSImage *)imageWithImageRep: (NSImageRep *)rep {
 	NSImage *result;
 
 	NSParameterAssert(rep);
 
 	result = [[NSImage alloc] initWithSize: [rep size];
 	[result addRepresentation: rep];
 	return [result autorelease];
 }
 @end



An example of usage:

 [[NSImage imageWithImageRep: someImageRep] compositeToPoint:NSZeroPoint operation:NSCompositeSourceOver];

