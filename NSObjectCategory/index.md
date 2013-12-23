---
layout: page
title: NSObjectCategory
---



** NSObject category CocoaDevUsersAdditions:**

** NSObject +CocoaDevUsersAdditions.h**
    
 #import <Foundation/Foundation.h>
 
 @interface NSObject (CocoaDevUsersAdditions)
 
 // NOTE: results array contains instance of NSNull where result of performing selector is nil
 // a collection here is anything responding to -objectEnumerator
 - (void)performSelector:(SEL)aSelector withEachObjectFrom:(id)collection;
 - (NSArray *)resultsOfPerformingSelector:(SEL)aSelector withEachObjectFrom:(id)collection;
 
 - (void)log; //A **very** commonly used method, make it part of your default application project in PB or XC
 
 - (id)arrayByAddingObject:(id)anObject; // a convenience for those of us who like to avoid special cases for collections
 
 @end

**NSObject+CocoaDevUsersAdditions.m**
    
 #import "NSObject+CocoaDevUsersAdditions.h"
 
 @implementation NSObject (CocoaDevUsersAdditions)
 
 - (void)performSelector:(SEL)aSelector withEachObjectFrom:(id)collection
 {
     NSEnumerator *objectEnumerator = [collection objectEnumerator];
     id object;
 
     while(object = [objectEnumerator nextObject])
     {
        [self performSelector:aSelector withObject:object];
     }
 }
 
 - (NSArray *)resultsOfPerformingSelector:(SEL)aSelector withEachObjectFrom:(id)collection
 {
     NSEnumerator *objectEnumerator = [collection objectEnumerator];
     NSMutableArray *results = [NSMutableArray array];
     id object, result;
 
     while(object = [objectEnumerator nextObject])
     {
         result = [self performSelector:aSelector withObject:object];
         if (result == nil)
             result = [NSNull null];
         [results addObject:result];
     }
     return results;
 }
 
 - (void)log
 {
     NSLog(@"%@",self);
 }
 
 - (id)arrayByAddingObject:(id)anObject
 {
     return [NSArray arrayWithObjects:self, anObject, nil]; // NSArray will still behave normally, too
 }
 @end



Category:CocoaDevUsersAdditions

