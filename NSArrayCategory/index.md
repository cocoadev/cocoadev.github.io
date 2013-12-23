---
layout: page
title: NSArrayCategory
---



Some handy addenda to NSArray, everybody's favorite Cocoa OrderedCollection.  Some are for symmetry, some are for kicks, all are for free.  Enjoy.

**Source for NSArray+CocoaDevUsersAdditions.h :**
    
 #import <Foundation/Foundation.h>
 
 @interface NSArray(CocoaDevUsersAdditions)
 
 - (NSIndexSet *) indexesForObjects: (NSArray *) array; // returns the indexes which match objects
 
 + (NSArray *)arrayWithObjectsFromArrays:(NSArray *)arrays; //for handiness, especially with HigherOrderMessages
 + (NSArray *)arrayWithClonesOf:(id)object count:(unsigned)count; //for things like FlywheelPattern or maybe a RunArray implementation.
 
 /* please criticize these.... */
 + (NSArray *)arrayWithCRLFLinesOfFile:(NSString *)filePath; // assumes CRLF
 + (NSArray *)arrayWithLinesOfFile:(NSString *)filePath lineEnding:(NSString *)lineEnding;
 
 
 - (BOOL)isEmpty;
 - (id)firstObject;
 
 // Unnecessary; can use -doesContain from NSComparisonMethods.
 - (BOOL)containsObjectIdenticalTo:(id)anObject;
 
 - (NSArray *)arrayByRemovingFirstObject;
 - (NSArray *)arrayByRemovingLastObject;
 - (NSArray *)arrayByRemovingObjectAtIndex:(unsigned)index;
 - (NSArray *)arrayByRemovingObjectsInRange:(NSRange)range;
 - (NSArray *)arrayByRemovingObject:(id)anObject;
 - (NSArray *)arrayByRemovingObjectsFromArray:(NSArray *)unwanted;
 
 - (NSArray *)choppedAtCount:(unsigned)count;
 - (NSArray *)reversedArray;
 
 // NOTE: results array contains instance of NSNull where result of performing selector is nil
 // a collection here is anything responding to -objectEnumerator
 - (NSArray *)resultsOfMakeObjectsPerformSelector:(SEL)aSelector;
 - (NSArray *)resultsOfMakeObjectsPerformSelector:(SEL)aSelector withObject:(id)anObject;
 - (void)makeObjectsPerformSelector:(SEL)aSelector withRespectiveObjectsFrom:(id)collection;
 - (NSArray *)resultsOfMakeObjectsPerformSelector:(SEL)aSelector withRespectiveObjectsFrom:(id)collection;
 
 // Quick-and-dirty NSTableView delegate methods
 // useful mainly for uncomplicated single-column tableViews
 - (int)numberOfRowsInTableView:(NSTableView *)aTableView;
 
 - (id)tableView:(NSTableView *)aTableView
 objectValueForTableColumn:(NSTableColumn *)aTableColumn
 row:(int)rowIndex;
 
 @end


**Source for NSArray+CocoaDevUsersAdditions.m :**
    
 #import "NSArray+CocoaDevUsersAdditions.h"
 
 @implementation NSArray(CocoaDevUsersAdditions)
 
 - (NSIndexSet *) indexesForObjects: (NSArray *) array
 {
   NSMutableIndexSet * indexSet = [NSMutableIndexSet indexSet];
   NSUInteger index;
   for (id obj in array)
   {
     index = [self indexOfObject:obj];
     if (index != NSNotFound) [indexSet addIndex:index];
   }
   return [NSIndexSet alloc] initWithIndexSet:indexSet] autorelease];
 }
 
 + (NSArray *)arrayWithObjectsFromArrays:(NSArray *)arrays
 {
   NSEnumerator * arrayEnumerator = [arrays objectEnumerator];
   NSMutableArray * resultArray = [NSMutableArray new];
   NSArray * current;
   while(current = [arrayEnumerator nextObject])
   {
     [resultArray addObjectsFromArray:current];
   }
   return [NSArray arrayWithArray:resultArray];
 }
 
 +(NSArray *)arrayWithClonesOf:(id)object count:(unsigned)count
 {
   NSMutableArray * cloneArray = [NSMutableArray arrayWithCapacity:count];
   while([cloneArray count] < count)
   {
     [cloneArray addObject:object];
   }
   return [NSArray arrayWithArray:cloneArray];
 }
 
 + (NSArray *)arrayWithCRLFLinesOfFile:(NSString *)filePath; // assumes CRLF
 {
   return [NSArray arrayWithLinesOfFile:filePath lineEnding:@"\r\n"];
 }
 
 + (NSArray *)arrayWithLinesOfFile:(NSString *)filePath lineEnding:(NSString *)lineEnding
 {
   NSArray * result = nil;
   NSString * theFile = [NSString stringWithContentsOfFile:filePath];
   if (theFile == nil) // no string
     return result; // no result.
   
   NSArray * linesArray = [theFile componentsSeparatedByString:lineEnding];
   if ([linesArray count] > 0)
     result = [NSArray arrayWithArray:linesArray];
 	return result;
 }
 
 
 -(BOOL)isEmpty
 {
   return [self count] == 0;
 }
 
 -(BOOL)isNotEmpty
 {
   return [self count] != 0;
 }
 
 -(id)firstObject
 {
   return [self objectAtIndex:0];
 }
 
 - (BOOL)containsObjectIdenticalTo:(id)anObject
 {
   return (NSNotFound != [self indexOfObjectIdenticalTo:anObject]);
 }
 
 - (id)arrayByRemovingFirstObject;
 {
   if ([self count] == 0) return [[self copy] autorelease]; // self might have shorter lifetime than the autorelease pool; self might be mutable
   return [self subarrayWithRange:NSMakeRange(1, [self count] - 1)];
 }
 
 - (id)arrayByRemovingLastObject;
 {
   if ([self count] == 0) return [[self copy] autorelease];
   return [self subarrayWithRange:NSMakeRange(0, [self count] - 1)];
 }
 
 -(NSArray *)arrayByRemovingObjectAtIndex:(unsigned)index
 {
   NSMutableArray * newArray = [NSMutableArray arrayWithArray:self];
   [newArray removeObjectAtIndex:index];
   return [NSArray arrayWithArray:newArray];
 }
 
 -(NSArray *)arrayByRemovingObjectsInRange:(NSRange)range
 {
   NSMutableArray * newArray = [NSMutableArray arrayWithArray:self];
   [newArray removeObjectsInRange:range];
   return [NSArray arrayWithArray:newArray];
 }
 
 -(NSArray *)arrayByRemovingObject:(id)anObject
 {
   if(anObject == nil) { return [[self copy] autorelease]; } //dodge an exception
   NSMutableArray * newArray = [NSMutableArray arrayWithArray:self];
   [newArray removeObject:anObject];
   return [NSArray arrayWithArray:newArray];
 }
 
 - (NSArray *)arrayByRemovingObjectsFromArray:(NSArray *)unwanted
 {
   NSMutableArray * newArray = [NSMutableArray arrayWithArray:self];
   [newArray removeObjectsInArray:unwanted];
   return [NSArray arrayWithArray:newArray];
 }
 
 - (NSArray *)choppedAtCount:(unsigned)count
 {
   return [self subarrayWithRange:NSMakeRange(0, count)];
 }
 
 -(NSArray *)reversedArray
 {
   return [[self reverseObjectEnumerator] allObjects];
 }
 
 - (NSArray *)resultsOfMakeObjectsPerformSelector:(SEL)aSelector
 {
   NSMutableArray *result = [NSMutableArray arrayWithCapacity:[self count;
   NSEnumerator *objectEnumerator;
   id obj;
   id objResult;
   
   if (aSelector == NULL)
   {
     [NSException raise:NSInvalidArgumentException format:@"aSelector cannot be NULL."];
   }
   
   objectEnumerator = [self objectEnumerator];
   while (obj = [objectEnumerator nextObject])
   {
     objResult = [obj performSelector:aSelector];
     if (objResult == nil)
       objResult = [NSNull null];
     [result addObject:objResult];
   }
   
   return result;
 }
 
 - (NSArray *)resultsOfMakeObjectsPerformSelector:(SEL)aSelector withObject:(id)anObject
 {
   NSMutableArray *result = [NSMutableArray arrayWithCapacity:[self count]];
   NSEnumerator *objectEnumerator;
   id obj;
   id objResult;
   
   if (aSelector == NULL)
   {
     [NSException raise:NSInvalidArgumentException format:@"aSelector cannot be NULL."];
   }
   
   objectEnumerator = [self objectEnumerator];
   while (obj = [objectEnumerator nextObject])
   {
     objResult = [obj performSelector:aSelector withObject:anObject];
     if (objResult == nil)
       objResult = [NSNull null];
     [result addObject:objResult];
   }
   
   return result;
 }
 
 - (void)makeObjectsPerformSelector:(SEL)aSelector
 withRespectiveObjectsFrom:(id)collection
 {
   NSEnumerator *targetEnumerator = [self objectEnumerator];
   NSEnumerator *argumentEnumerator = [collection objectEnumerator];
   id target;
   
   while (target = [targetEnumerator nextObject])
   {
     [target performSelector:aSelector withObject:[argumentEnumerator nextObject]];
   }
 }
 
 - (NSArray *)resultsOfMakeObjectsPerformSelector:(SEL)aSelector
 withRespectiveObjectsFrom:(id)collection
 {
   NSEnumerator *targetEnumerator = [self objectEnumerator];
   NSEnumerator *argumentEnumerator = [collection objectEnumerator];
   NSMutableArray *results = [NSMutableArray arrayWithCapacity:[self count]];
   id target;
   id result;
   
   while (target = [targetEnumerator nextObject])
   {
     result = [target performSelector:aSelector withObject:[argumentEnumerator nextObject]];
     if (result == nil)
       result = [NSNull null];
     [results addObject:result];
   }
   
   return results;
 }
 
 // Quick-and-dirty NSTableView delegate methods
 // useful mainly for uncomplicated single-column tableViews
 - (int)numberOfRowsInTableView:(NSTableView *)aTableView
 {
   return [self count];
 }
 
 - (id)tableView:(NSTableView *)aTableView
 objectValueForTableColumn:(NSTableColumn *)aTableColumn
 row:(int)rowIndex
 {
   return [self objectAtIndex:rowIndex];
 }
 
 
 @end



Category:CocoaDevUsersAdditions

