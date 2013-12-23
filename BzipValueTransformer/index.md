---
layout: page
title: BzipValueTransformer
---

An NSValueTransformer that decompresses NSData into an NSString using my NSData+Bzip2 category (see NSDataPlusBzip). It is reversible. � BryanWoods

    
// Bzip2ValueTransformer.h
@interface Bzip2ValueTransformer : NSValueTransformer

@end

//  Bzip2ValueTransformer.m
#import "Bzip2ValueTransformer.h"
#import "NSData+Bzip2.h"

@implementation Bzip2ValueTransformer

+ (Class) transformedValueClass
{
	return [NSString class];
}

+ (BOOL) allowsReverseTransformation
{
	return YES;
}

- (id) transformedValue:(id)data
{
	// decompress
	return [[[NSString alloc] initWithData:[data bunzip2] encoding:NSUTF8StringEncoding] autorelease];
}

- (id) reverseTransformedValue:(id)string
{
	// compress
	return string dataUsingEncoding:NSUTF8StringEncoding] bzip2];
}

@end


----

To give an idea of how it's used, my test project is just a Cocoa Document-based Application with an [[NSTextView bound (�la CocoaBindings) to an NSData in MyDocument. Selecting Bzip2ValueTransformer in the Bindings palette in Interface Builder handles all of the heavy lifting.

    
// MyDocument.h
@interface MyDocument : NSDocument
{
	NSData * text;
}

@end


// MyDocument.m
#import "MyDocument.h"
#import "Bzip2ValueTransformer.h"

@implementation MyDocument

+ (void) initialize
{
	[NSValueTransformer setValueTransformer:Bzip2ValueTransformer new] autorelease] forName:@"Bzip2ValueTransformer"];
}

- (void) dealloc
{
	[text release];
	[super dealloc];
}

- ([[NSString *) windowNibName
{
    return @"MyDocument";
}

- (NSData *) dataRepresentationOfType:(NSString *)type
{
	return text;
}

- (BOOL) loadDataRepresentation:(NSData *)data ofType:(NSString *)type
{
	text = [data retain];
	return (text != nil);
}

@end

