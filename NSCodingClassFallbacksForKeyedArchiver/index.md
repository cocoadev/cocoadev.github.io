---
layout: page
title: NSCodingClassFallbacksForKeyedArchiver
---




I'd need to have a mean to unarchive an object using another class when initial class is not available. + [NSObject classFallbacksForKeyedArchiver] seems to be the solution, but it does not work for me.

Here is a sample code

**Project Archive create.**

Main.m
    
#import <Foundation/Foundation.h>
#include "Create.h"

int main (int argc, const char * argv[]) {
    NSAutoreleasePool * pool = [[NSAutoreleasePool alloc] init];
	Create * obj = Create alloc] init];
	[obj setStringValue:@"Hello World"];
    [[[NSKeyedArchiver archiveRootObject:obj toFile:@"/tmp/test"];
    [pool release];
    return 0;
}



Create.h
    
#import <Cocoa/Cocoa.h>


@interface Create : NSObject {
	NSString * stringValue;
}
- (NSString *)stringValue;
- (void)setStringValue:(NSString *)value;


@end


Create.m
    
#import "Create.h"


@implementation Create
+ (NSArray *)classFallbacksForKeyedArchiver
{
	return [NSArray arrayWithObject:@"Restore"];
}

- (NSString *)stringValue {
    return stringValue retain] autorelease];
}

- (void)setStringValue:([[NSString *)value {
    if (stringValue != value) {
        [stringValue release];
        stringValue = [value copy];
    }
}

#pragma mark -
#pragma mark archiving

- (void)encodeWithCoder:(NSCoder *)coder 
{
    [coder encodeObject:[self stringValue] forKey:@"stringValue"];
}

- (id)initWithCoder:(NSCoder *)coder 
{
    if (self = [self init]) {
        [self setStringValue:[coder decodeObjectForKey:@"stringValue"]];
    }
    return self;
}
@end


**Project Archvive Restore**

Main.m
    
#import <Foundation/Foundation.h>

int main (int argc, const char * argv[]) {
    NSAutoreleasePool * pool = [[NSAutoreleasePool alloc] init];

    // insert code here...
	id restore = [NSKeyedUnarchiver unarchiveObjectWithFile:@"/tmp/test"];
    NSLog([restore stringValue]);
    [pool release];
    return 0;
}



Restore.h
    
#import <Cocoa/Cocoa.h>


@interface Restore : NSObject {
	NSString * stringValue;
}
- (NSString *)stringValue;
- (void)setStringValue:(NSString *)value;


@end


Restore.m
    
#import "Restore.h"


@implementation Restore
- (NSString *)stringValue {
    return stringValue retain] autorelease];
}

- (void)setStringValue:([[NSString *)value {
    if (stringValue != value) {
        [stringValue release];
        stringValue = [value copy];
    }
}

#pragma mark -
#pragma mark archiving

- (void)encodeWithCoder:(NSCoder *)coder 
{
    [coder encodeObject:[self stringValue] forKey:@"stringValue"];
}

- (id)initWithCoder:(NSCoder *)coder 
{
    if (self = [super init]) {
        [self setStringValue:[coder decodeObjectForKey:@"stringValue"]];
    }
    return self;
}
@end


----

I finaly used:

- (Class)classForArchiver

But i'm still wondering why previous method didn't work

----
Just to ask a stupid question, are you using Tiger?

"*Available in Mac OS X v10.4 and later.*"
----
Yes. This is for a Tiger and up only Application.
----
You didn't say how it doesn't work. classFallbacksForKeyedArchiver is called durring encoding, and the information is stored in the archive. The information will only be used at decode time if your class, create, doesn't exist.

----

Note that NSKeyedUnarchiver can have a delegate. The delegate can implement - (Class)	unarchiver:(NSKeyedUnarchiver*) unarchiver cannotDecodeObjectOfClassName:(NSString*) name originalClasses:(NSArray*) classNames

This allows you to substitute one class for another during unarchiving. For example it comes in handy if you change the name of a class but have old files containing the old name. GrahamCox.

