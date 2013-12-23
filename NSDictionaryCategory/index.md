---
layout: page
title: NSDictionaryCategory
---



The just added methods are rather overly complicated..  I think I can fix hasKey without testing in ProjectBuilder, but for the others I'm too chicken.  If I screw it up anyway, well, there's the history. :)

By the way, to whoever coded hasKey:  I suspect you did it the way you did because you were worried about keys whose value is nil?  Worry not, NSDictionaries can't hold nil as a value.

*Through the magic of toll-free bridging, it can, since CFDictionary can. Of course, you would be perfectly justified in assuming your NSDictionaries are normal and can't hold nil. I don't know how you could check for it without dropping down to CFDictionary calls anyway.*

**NSDictionary_CocoaDevUsersAdditions.h**
    
 //NSDictionary_CocoaDevUsersAdditions.h
 
 #import <Foundation/Foundation.h>
 
 @interface NSDictionary (CocoaDevUsersAdditions)
 
 -(NSDictionary *)dictionaryBySettingObject:(id)object forKey:(id<NSCopying>)key;
 -(NSDictionary *)dictionaryBySettingObjects:(NSArray *)someObjects forKeys:(NSArray *)keys;
 -(NSDictionary *)dictionaryByAddingEntriesFromDictionary:(NSDictionary *)otherDictionary;
 
 -(BOOL) boolForKey: (id) aKey;
 -(int) intForKey: (id) aKey;
 -(BOOL) hasKey: (id) aKey;
 
 -(NSArray *)allObjectsSortedByKeySelector:(SEL)sortSelector;
 
 // Requires NSArrayCategory
 - (NSDictionary *)resultsOfMakeObjectsPerformSelector:(SEL)selector;
 - (NSDictionary *)resultsOfMakeObjectsPerformSelector:(SEL)selector withObject:(id)object;
 @end


**NSDictionary_CocoaDevUsersAdditions.m**
    
 //NSDictionary_CocoaDevUsersAdditions.m
 #import "NSDictionary_CocoaDevUsersAdditions.h"
 
 @implementation NSDictionary (CocoaDevUsersAdditions)
 
 -(NSDictionary *)dictionaryBySettingObject:(id)object forKey:(id<NSCopying>)key
 {
   return [self dictionaryByAddingEntriesFromDictionary:[NSDictionary dictionaryWithObject:object forKey:key]];
 }
 
 -(NSDictionary *)dictionaryBySettingObjects:(NSArray *)someObjects forKeys:(NSArray *)keys
 {
   return [self dictionaryByAddingEntriesFromDictionary:[NSDictionary dictionaryWithObjects:someObjects forKeys:keys]];
 }
 
 -(NSDictionary *)dictionaryByAddingEntriesFromDictionary:(NSDictionary *)otherDictionary
 {
   NSMutableDictionary * returnDictionary = [NSMutableDictionary dictionaryWithDictionary:self];
   [returnDictionary addEntriesFromDictionary:otherDictionary];
   return returnDictionary;
 }
 
 -(BOOL) boolForKey: (id) aKey
 {
   BOOL result = NO; // lame default return value
   id <NSObject> obj = [self objectForKey:aKey];
   if (obj) {
     SEL bv = @selector(boolValue);
     if ([obj respondsToSelector:bv])
       result = ([obj performSelector:bv] ? YES : NO);
     else if ([obj isKindOfClass:[NSString class]]) {
       result = ([(NSString *)obj caseInsensitiveCompare: @"YES"] == NSOrderedSame);
       if (!result)
         result = ([(NSString *)obj caseInsensitiveCompare: @"TRUE"] == NSOrderedSame);
     }
   }
   return result;
 }
 
 -(int) intForKey: (id) aKey
 {
   int result = 0; // lame default return value
   id <NSObject> obj = [self objectForKey:aKey];
   if (obj) {
     SEL iv = @selector(intValue);
     if ([obj respondsToSelector:iv])
       result = (int)[obj performSelector:iv];
   }
   return result;
 }
 
 -(BOOL) hasKey: (id) testKey
 {
   return ([self objectForKey:testKey] != nil);
 }
 
 -(NSArray *)allObjectsSortedByKeySelector:(SEL)sortSelector
 {
   NSArray *sortedKeys = self allKeys] sortedArrayUsingSelector:sortSelector];
   NSArray *resultArray = [self objectsForKeys:sortedKeys notFoundMarker:[NSNull null;
   return resultArray;
 }
 
 - (NSDictionary *)resultsOfMakeObjectsPerformSelector:(SEL)selector
 {
   NSArray *keys = [self allKeys];
   NSArray *values = [self objectsForKeys:keys notFoundMarker:nil];
   return [NSDictionary dictionaryWithObjects:[values resultsOfMakeObjectsPerformSelector:selector] forKeys:keys];
 }
 
 - (NSDictionary *)resultsOfMakeObjectsPerformSelector:(SEL)selector withObject:(id)object
 {
   NSArray *keys = [self allKeys];
   NSArray *values = [self objectsForKeys:keys notFoundMarker:nil];
   return [NSDictionary dictionaryWithObjects:[values resultsOfMakeObjectsPerformSelector:selector withObject:object] forKeys:keys];
 }
 
 @end



Category:CocoaDevUsersAdditions

