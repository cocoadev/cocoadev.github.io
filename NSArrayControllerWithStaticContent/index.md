---
layout: page
title: NSArrayControllerWithStaticContent
---

I've found that you sometimes need static content in an array controller, which doesn't seem possible without subclassing NSArrayController.

An example of this is the "All Artists" entry at the top of the artist browse table in iTunes.

To achieve this, define an NSArrayController subclass, e.g. JHStaticContentArrayController:

    
@interface JHStaticContentArrayController : NSArrayController
{
	NSArray *staticContent;
}

- (NSArray *)staticContent;
- (void)setStaticContent:(NSArray *)aStaticContent;

@end


The setStaticContent: method obviously takes in an array of objects that you want added to the TOP (at least in this example) of the resulting content array. The magic happens in arrangeObjects:

    
- (NSArray *)arrangeObjects:(NSArray *)objects
{
	// Do the normal arranging first
	NSArray *arrangedObjects = [super arrangeObjects:objects];
	
	// If we don't have static content, proceed as normal
	if (![self staticContent])
		return arrangedObjects;
	
	// Otherwise add the static content to the start of the resulting array
	NSMutableArray *hybridArray = [[NSMutableArray alloc] init];
	[hybridArray addObjectsFromArray:[self staticContent]];
	[hybridArray addObjectsFromArray:arrangedObjects];
	return [hybridArray autorelease];
}


Some caveats:

* Probably won't play nice with sorting
* This implementation won't add the static content to anywhere but the start of the arrangedObjects array


-- JeremyHiggs

