---
layout: page
title: JpegTwoThousandValueTransformer
---

An NSValueTransformer that enables bindings to use Jpeg2000 compression rather than just the raw TIFF data. 

I've hand tuned the compression parameter to show no compression artifacts for my test images. You may wish to increase it. � BryanWoods

    
// Jpeg2kValueTransformer
@interface Jpeg2kValueTransformer : NSValueTransformer

@end

//  Jpeg2kValueTransformer.m
#import "Jpeg2kValueTransformer.h"

@implementation Jpeg2kValueTransformer

+ (Class) transformedValueClass;
{
    return [NSData class];
}

+ (BOOL) allowsReverseTransformation;
{
    return YES;
}

- (id) transformedValue:(id)value;
{
	return [[NSBitmapImageRep imageRepWithData:value] TIFFRepresentation];
}

- (id) reverseTransformedValue:(id)value;
{
	return [[NSBitmapImageRep imageRepWithData:value] representationUsingType:NSJPEG2000FileType properties:[NSDictionary dictionaryWithObject:[NSNumber numberWithFloat:0.4f] forKey:@"NSImageCompressionFactor"]];
}

@end



Note that NSJPEG2000FileType is only available in Mac OS X v10.4 and later.

----

Some notes


* Jpeg2000 seems to works best with large (width/height) images. Small images show more compression artifacts.
* This code base can easily be used to create PNGValueTransformer, GIFValueTransformer, etc, by changing NSJPEG2000FileType appropriately. (see http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/Classes/NSBitmapImageRep_Class/Reference/Reference.html)
* Any value transformer made this way can open images compressed by any other value transformer because the transformedValue: just calls NSBitmapImageRep directly; it will open any format NSBitmapImageRep knows how.

