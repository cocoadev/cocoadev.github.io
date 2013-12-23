---
layout: page
title: NSUserDefaultsCategory
---



Code for using custom types with the NSUserDefaults class.

Modified from the NSColor sample code on Apple's developer web site

**Source for NSUserDefaults+CocoaDevUsersAdditions.h :**
    
 #import <Foundation/Foundation.h>
 
 @interface NSUserDefaults(CocoaDevUsersAdditions)
 - (void)encodeObject:(id <NSCoding>)anObject forKey:(NSString*)aKey;
 - (id)decodeObjectForKey:(NSString*)aKey;
 @end

 
**Source for NSUserDefaults+CocoaDevUsersAdditions.m :**
    
 #import "NSUserDefaults+CocoaDevUsersAdditions.h"
 
 @implementation NSUserDefaults(CocoaDevUsersAdditions)
 
 - (void)encodeObject:(id <NSCoding>)anObject forKey:(NSString*)aKey
 {
 	// Requires 10.2 or later for keyed archiving
 	NSData* theData = [NSKeyedArchiver archivedDataWithRootObject:anObject];
 	[self setObject:theData forKey:aKey];
 }
 
 - (id)decodeObjectForKey:(NSString*)aKey
 {
 	// Requires 10.2 or later for keyed archiving
 	NSData* theData = [self dataForKey:aKey];
 	return (theData != nil) ? [NSKeyedUnarchiver unarchiveObjectWithData:theData] : nil;
 }
 @end



Category:CocoaDevUsersAdditions

