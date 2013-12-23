---
layout: page
title: CoreDataTools
---

A number of tools that makes dealing with Core Data objects easier in app-delegate-has-single-NSManagedObjectContext apps.

Docs inline with the code. Consider them public domain -- EmanueleVulcano aka millenomi

L0CoreDataTools.h:

    

#import <Cocoa/Cocoa.h>

@interface NSManagedObjectContext (L0CoreDataTools)

// Returns a default context. (The implementation here defaults to NSApp's delegate's
// managedObjectContext, but you can change it and all other methods will pick it up
// appropriately.)
+ (id) defaultContext;

@end

@interface NSFetchRequest (L0CoreDataTools)

// Executes this fetch request in the default managed object context.
- (NSArray*) executeAndReturnError:(NSError**) err;

@end

@interface NSEntityDescription (L0CoreDataTools)

// Returns an entity from the default managed object context.
+ (NSEntityDescription*) entityForName:(NSString*) name;

@end

// The following methods assume that you are using custom classes for your
// entities.
@interface NSManagedObject (L0CoreDataTools)

// Creates a new instance of this managed object with the entity returned
// by +entity and inserts it in the default managed object context.
- (id) init;

// Returns the entity attached to this class in the default managed object context.
// By default, returns the entity whose name is equal to the class (for example, for
// class AbcGizmo, it returns the entity named AbcGizmo).
+ (id) entity;

@end



L0CoreDataTools.m:

    

#import "L0CoreDataTools.h"

@implementation NSManagedObjectContext (L0CoreDataTools)

// tweak me if not storing your managed object context there.
+ (id) defaultContext {
	return [[NSApp delegate] managedObjectContext];
}

@end

@implementation NSFetchRequest (L0CoreDataTools)

- (NSArray*) executeAndReturnError:(NSError**) err {
	return [[NSManagedObjectContext defaultContext] executeFetchRequest:self error:err];
}

@end

@implementation NSEntityDescription (L0CoreDataTools)

+ (NSEntityDescription*) entityForName:(NSString*) name {
	return [self entityForName:name inManagedObjectContext:[NSManagedObjectContext defaultContext]];
}

@end

@implementation NSManagedObject (L0CoreDataTools)

- (id) init {
	return [self initWithEntity:self class] entity] insertIntoManagedObjectContext:[[[NSManagedObjectContext defaultContext]];
}

+ (id) entity {
	return [NSEntityDescription entityForName:NSStringFromClass(self)];
}

@end



