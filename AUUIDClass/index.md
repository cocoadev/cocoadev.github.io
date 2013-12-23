---
layout: page
title: AUUIDClass
---

This is a simple UUID class.
SimonAndreasMenke

----

AUUID.h

    

#import <Cocoa/Cocoa.h>


@interface AUUID : NSObject <NSCoding,NSCopying> {
	unsigned char uu[16];
}

+ (AUUID*)uuid;
+ (AUUID*)uuidWithStringValue:(NSString*)string;

- (id)init;
- (id)initWithStringValue:(NSString*)string;

- (NSString*)stringValue;

- (NSComparisonResult)compare:(AUUID*)uuid;
- (BOOL)isEqualToAUUID:(AUUID*)uuid;

- (void)encodeWithCoder:(NSCoder *)coder;
- (id)initWithCoder:(NSCoder *)coder;

@end



AUUID.m

    

#include <uuid/uuid.h>
#import "AUUID.h"

@interface AUUID (priv)
- (id)_initWithUUIDData:(char*)data;
- (unsigned char*)_uu;
@end

@implementation AUUID

+ (AUUID*)uuid
	{ return [[self class] alloc] init] autorelease]; }
+ (AUUID*)uuidWithStringValue:([[NSString*)string
	{ return [[self class] alloc] initWithStringValue:string] autorelease]; }

- (id)init {
	if ((self = [super init])) {
		uuid_generate(uu);
	}
	return self;
}

- (id)initWithStringValue:([[NSString*)string {
	if ((self = [super init])) {
		if (uuid_parse([string UTF8String], uu) != 0) {
			[self release];
			return nil;
		}
	}
	return self;
}

- (id)_initWithUUIDData:(char*)data {
	if ((self = [super init])) {
		strncpy((char*)uu,data,sizeof(uuid_t));
	}
	return self;
}

- (NSString*)stringValue {
	char str[37];
	uuid_unparse_upper(uu, str);
	return [[[NSString alloc] initWithUTF8String:str] autorelease];
}

- (NSComparisonResult)compare:(AUUID*)uuid {
	return uuid_compare(uu, [uuid _uu]);
}
- (BOOL)isEqualToAUUID:(AUUID*)uuid {
	return ([self compare:uuid] == NSOrderedSame);
}

- (NSString*)description {
	return [self stringValue];
}

- (unsigned char*)_uu {
	return uu;
}

- (void)encodeWithCoder:(NSCoder *)coder {
    if ([coder allowsKeyedCoding]) {
        [coder encodeObject:[NSData dataWithBytes:uu length:sizeof(uuid_t)] forKey:@"uuid"];
    } else {
        [coder encodeObject:[NSData dataWithBytes:uu length:sizeof(uuid_t)]];
    }
    return;
}

- (id)initWithCoder:(NSCoder *)coder {
    if ((self = [super init])) {
		if ( [coder allowsKeyedCoding] ) {
			NSData* data = coder decodeObjectForKey:@"uuid"] retain];
			[data getBytes:uu length:sizeof(uuid_t)];
			[data release];
		} else {
			[[NSData* data = coder decodeObject] retain];
			[data getBytes:uu length:sizeof(uuid_t)];
			[data release];
		}
	}
    return self;
}

- (id)copyWithZone:([[NSZone *)zone {
	return [[AUUID allocWithZone:zone] _initWithUUIDData:(char*)uu];
}

@end


