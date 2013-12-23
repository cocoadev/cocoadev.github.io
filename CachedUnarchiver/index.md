---
layout: page
title: CachedUnarchiver
---

Recently was using CoreData and a data attribute in an entity to store an icon.  I used a table and the NSUnarchivedData value transformer.  Displayed fine, but it was super slow when resizing the window and other refresh operations.  Turns out the data is actually unarchived every redraw, so I cooked up this simple transformer that uses a cache... so it only actually needs to unarchive once.  Granted, if the icon changes this might not be great -- but in my case the icon for each entity was static after the initial setting.

    static NSMutableDictionary *cache;

@implementation CachedUnarchiverValueTransformer

+ (void)initialize
{
	cache = [[NSMutableDictionary alloc]init];
	[cache retain];
}

+ (Class)transformedValueClass;
{ return [NSData class]; }

+ (BOOL)allowsReverseTransformation;
{ return NO; }

- (id)transformedValue:(id)value;
{       
	if (![cache objectForKey:value]) {
		id object = [NSUnarchiver unarchiveObjectWithData:value];
		[cache setObject:object forKey:value];
		return object;
	} else
		return [cache objectForKey:value];
}

@end

