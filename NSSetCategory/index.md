---
layout: page
title: NSSetCategory
---



People have expressed their desire for greater functionality from NSSet.  Anyone who wants to write a few of the methods (or improve existing methods) should feel more than welcome.

At least one person has said they want:


*- (NSSet *)intersectionWithSet:(NSSet *)aSet;
*- (NSSet *)setByAddingSet:(NSSet *)aSet;
*- (NSSet *)setByRemovingSet:(NSSet *)aSet;
*- (NSSet *)setByAddingObject:(id)anObject;
*- (NSSet *)setByRemovingObject:(id)anObject;


----
**NSSet category CocoaDevUsersAdditions:**

**NSSet+CocoaDevUsersAdditions.h**
    
 #import <Foundation/Foundation.h>
 
 @interface NSSet (CocoaDevUsersAdditions)
 
 - (NSSet *)setByUnioningSet:(NSSet *)aSet;
 - (NSSet *)setByMinusingSet:(NSSet *)aSet;
 - (NSSet *)setByIntersectingSet:(NSSet *)aSet;
 - (NSSet *)setByComplementingIntersectionWithSet:(NSSet *)aSet;
 - (NSSet *)setByAddingObject:(id)anObject;
 - (NSSet *)setByAddingObjectsFromArray:(NSArray *)anArray;
 - (NSSet *)setByRemovingObject:(id)anObject;
 
 // NOTE: results set contains instance of NSNull if result of performing selector with some object is nil
 - (NSSet *)resultsOfMakeObjectsPerformSelector:(SEL)aSelector;
 - (NSSet *)resultsOfMakeObjectsPerformSelector:(SEL)aSelector withObject:(id)anObject;
 
 @end



**NSSet+CocoaDevUsersAdditions.m**
    
 #import "NSSet+CocoaDevUsersAdditions.h"
 
 @implementation NSSet (CocoaDevUsersAdditions)
 
 - (NSSet *)setByUnioningSet:(NSSet *)aSet
 {
   id ret = self mutableCopy] autorelease];
   [ret unionSet:aSet];
   return ret;
 }
 - (NSSet *)setByMinusingSet:(NSSet *)aSet
 {
   id ret = [[self mutableCopy] autorelease];
   [ret minusSet:aSet];
   return ret;
 }
 - (NSSet *)setByIntersectingSet:(NSSet *)aSet
 {
   id ret = [[self mutableCopy] autorelease];
   [ret intersectSet:aSet];
   return ret;
 }
 - (NSSet *)setByComplementingIntersectionWithSet:(NSSet *)aSet
 {
   id ret = [[self mutableCopy] autorelease];
   [ret unionSet:aSet];
   [ret minusSet:[self setByIntersectingSet:aSet;
   return ret;
 }
 - (NSSet *)setByAddingObject:(id)anObject
 {
   id ret = self mutableCopy] autorelease];
   [ret addObject:anObject];
   return ret;
 }
 - (NSSet *)setByAddingObjectsFromArray:(NSArray *)anArray
 {
   id ret = [[self mutableCopy] autorelease];
   [ret addObjectsFromArray:anArray];
   return ret;
 }
 - (NSSet *)setByRemovingObject:(id)anObject
 {
   id ret = [[self mutableCopy] autorelease];
   [ret removeObject:anObject];
   return ret;
 }
 - (NSSet *)resultsOfMakeObjectsPerformSelector:(SEL)aSelector
 {
   NSMutableSet *result = [NSMutableSet set];
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
 - (NSSet *)resultsOfMakeObjectsPerformSelector:(SEL)aSelector withObject:(id)anObject
 {
   NSMutableSet *result = [NSMutableSet set];
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
 @end


----


[[Category:CocoaDevUsersAdditions

