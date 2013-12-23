---
layout: page
title: NSMutableArrayCategory
---



Sure looks bare up here with no comments.

OK, here's one:

**19 March 2009** - Added     moveObject: toIndex: (Just to keep things alive!) -- *this had a bug in it, now fixed --JediKnil*

**4 June 2010** -     moveObject: toIndex: (Fixed yet again!)

----
Source for NSMutableArray +CocoaDevUsersAdditions.h :
    
 #import <Foundation/Foundation.h>
 
 @interface NSMutableArray (CocoaDevUsersAdditions)
 
 - (void)push:(id)anObject;
 - (id)pop;
 - (void)insertObjectsFromArray:(NSArray *)anArray atIndex:(int)index;
 
 - (void)replaceObject:(id)oldObject withObject:(id)newObject;
 
 - (void)moveObject:(id)object toIndex:(NSUInteger)toIndex;
 
 // Method for serializing an NSMutableArray object to a NSData in the form of a property list
 - (NSData *)asDataReturningError:(NSString **)error;
 // Method for retrieving an NSMutableArray object from an property-list-formatted NSData object
 + (NSMutableArray *)arrayWithData:(NSData *)data;
 
 @end

----
Source for NSMutableArray+CocoaDevUsersAdditions.m :
    
 #import "NSMutableArray+CocoaDevUsersAdditions.h"
 
 @implementation NSMutableArray (CocoaDevUsersAdditions)
 
 - (void)push:(id)anObject
 {
   [self addObject:anObject];
 }
 - (id)pop
 {
   id res;
   if ([self count] == 0)
   {
     return nil;
   }
   res = [self lastObject] retain] autorelease];
   [self removeLastObject];
   return res;
 }
 
 -(void)insertObjectsFromArray:(NSArray *)anArray atIndex:(int)index
 {
   int oldCount = [self count];
   int cursor = index;
   int i;
   
   [self addObjectsFromArray:anArray];
   
   for (i = oldCount; i < [self count]; i++)
   {
 	[self exchangeObjectAtIndex:i withObjectAtIndex: cursor];
 	cursor++;
   }
   
   // The above doesn't work for arbitrary insertions!?! I propose instead:
   // NSMutableArray* newOrdering = [NSMutableArray array];
   // [newOrdering addObjectsFromArray:[self subarrayWithRange:NSMakeRange(0, anIndex);
   // [newOrdering addObjectsFromArray:anArray];
   // [newOrdering addObjectsFromArray:[self subarrayWithRange:NSMakeRange(anIndex, [self count]-anIndex)]];
   // [self setArray:newOrdering];
 }
 
 - (void)replaceObject:(id)oldObject withObject:(id)newObject
 {
   NSRange range = NSMakeRange(0, [self count]); // Efficiency
   int index = [self indexOfObject:oldObject];
   
   while (index != NSNotFound) {
     [self replaceObjectAtIndex:index withObject:newObject];
     
     // Efficiency
     range.length -= (index - range.location);
     range.location = index;
     index = [self indexOfObject:oldObject inRange:range];
   }
 }
 
 - (void)moveObject:(id)object toIndex:(NSUInteger)toIndex {
   NSUInteger currentIndex = [self indexOfObject:object];
   [self insertObject:object atIndex:(toIndex>currentIndex)?toIndex+1:toIndex];
   if (currentIndex!=NSNotFound) {
     [self removeObjectAtIndex:(currentIndex < toIndex ? currentIndex : currentIndex + 1)];
   }
 }
 
 - (NSData *)asDataReturningError:(NSString **)error
 {
   return [NSPropertyListSerialization dataFromPropertyList:self format:NSPropertyListXMLFormat_v1_0 errorDescription:error];
 }
 
 + (NSMutableArray *)arrayWithData:(NSData *)data
 {
   NSString *sData = [NSString allocWithZone:[self zone initWithData:data encoding:NSUTF8StringEncoding] autorelease] ;
   if (sData != nil)
   {
     NSArray *thePropertyList = [sData propertyList];
     if (thePropertyList != nil)
       return thePropertyList mutableCopy] autorelease];
   }
   return nil;
 }
 
 @end



[[Category:CocoaDevUsersAdditions

