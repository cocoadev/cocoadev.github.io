---
layout: page
title: BindingToSet
---

Recently I needed to bind an NSTextCell NSTableView column to a to-many property of a CoreData entity.  After banging my head against the wall for a bit when Cocoa refused to bind to the relationship as it can't possibly be KVC-compliant, I came up with the idea of using an NSValueTransformer to transform the set to a string.  Here's my value transformer:

    %
// CDSetToStringTransformer.h
#import <Cocoa/Cocoa.h>

@interface CDSetToStringTransformer : NSValueTransformer
{
	NSString *_keyPath;
}

- (id)initWithKeyPath:(NSString *)keyPath;

- (NSString *)keyPath;
- (void)setKeyPath:(NSString *)keyPath;

@end

// CDSetToStringTransformer.m
#import "CDSetToStringTransformer.h"

@implementation CDSetToStringTransformer

+ (Class)transformedValueClass
{
	return [NSString class];
}

+ (BOOL)allowsReverseTransformation
{
	return NO;
}

- (id)initWithKeyPath:(NSString *)keyPath
{
	if(self = [super init])
		[self setKeyPath:keyPath];
	
	return self;
}

- (void)dealloc
{
	[self setKeyPath:nil];
	
	[super dealloc];
}

- (NSString *)keyPath
{
	return _keyPath;
}

- (void)setKeyPath:(NSString *)newKeyPath
{
	newKeyPath = [newKeyPath copy];
	[_keyPath release];
	_keyPath = newKeyPath;
}

- (id)transformedValue:(id)value
{
	NSSet *sourceSet = (NSSet *)value;
	if([sourceSet count] == 0)
		return [NSNull null];
	
	NSString *result;
	NSEnumerator *objectEnum = [sourceSet objectEnumerator];
	result = objectEnum nextObject] valueForKeyPath:[self keyPath;
	
	id currentObject;
	while(currentObject = [objectEnum nextObject])
		result = [result stringByAppendingFormat:@", %@", [currentObject valueForKeyPath:[self keyPath]]];
	
	return result;
}

@end


Put the instantiation in the proper place (I'm using     + [AppDelegate initialize]).  Initialize the transformer using     - [CDSetToStringTransformer initWithKeyPath:], where keyPath is the key path *relative to each object in the set* that you want to coalesce into one string.

Beware that this class is not localized, but localization should be trivial.

