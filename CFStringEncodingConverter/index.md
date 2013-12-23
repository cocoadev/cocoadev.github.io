---
layout: page
title: CFStringEncodingConverter
---

I use the following function in my application:

(header file can be found at: http://www.opendarwin.org/cgi-bin/cvsweb.cgi/src/CoreFoundation/StringEncodings.subproj/CFStringEncodingConverter.h?rev=1.1.1.3 )

    

extern UInt32 CFStringEncodingUnicodeToBytes(
    UInt32 encoding, 
    UInt32 flags, 
    const UniChar *characters, 
    UInt32 numChars, 
    UInt32 *usedCharLen, 
    UInt8 *bytes, 
    UInt32 maxByteLen, 
    UInt32 *usedByteLen);

if (CFStringEncodingUnicodeToBytes(encoding, (1 << 6), &character, 1, &ucl, NULL, 0, &ubl) == 0)
{
   // Some code
}
else
{
   // Some other code
}


It works just fine, but:

A) Is it a private header file(to me it doesn't seem to be)? How to include it properly so it doesn't give me a warning?

B) It's undocumented?

C) Is there a better way to check if a character is available in the specified encoding?

-- JP

----

Stuff the character into an NSString and the call -canBeConvertedToEncoding: on it.  -- Bo

----

Yeah, but thats not very efficent when you have to do that upto 4000 times.

-- JP

----

I doubt that will be a problem; I benchmarked checking all 65536 possible values of the unichar type and it took under half a second on my computer.  Checking a 'mere' 4000 values took 1/20 of a second.  I've included the whole main function below so you can try it out on yours (just create a Foundation Tool project and paste it into main.m, replacing the default main function) -- Bo
    
int main (int argc, const char * argv[]) {
	NSAutoreleasePool * pool = [[NSAutoreleasePool alloc] init];
	unichar c;
	NSString* charString;
	NSMutableString* validString = [[NSMutableString alloc] init];
	NSMutableString* invalidString = [[NSMutableString alloc] init];
	NSDate* startDate = [NSDate date];
	for (c = 0; c < 65535; c++) {
		charString = [[NSString alloc] initWithCharacters:&c length:1];
		BOOL valid = [charString canBeConvertedToEncoding:NSMacOSRomanStringEncoding];
		if (valid) {
			[validString appendString:charString];
		} else {
			[invalidString appendString:charString];
		}
		[charString release];
	}
	NSDate* stopDate = [NSDate date];
	NSTimeInterval interval = [stopDate timeIntervalSinceDate:startDate];
	NSLog(@"stop Date: %@\ntime to execute: %f secs", stopDate, interval);
	NSLog(@"number of valid chars: %d\nnumber of invalid chars: %d", [validString length], [invalidString length]);
    [pool release];
    return 0;
}


----

I tested both versions and:

A) COCOA_WAY

*time to execute: 0.166083 secs
*number of valid chars: 257
*number of invalid chars: 65278


B) !COCOA_WAY

*time to execute: 0.021796 secs
*number of valid chars: 257
*number of invalid chars: 65278


Conclusion: Cocoa version is about 8 times slower, BUT 0.166 sec isn't very bad!!! -- JP

    
#import <Foundation/Foundation.h>
#include <CoreFoundation/CoreFoundation.h>

#define COCOA_WAY

int main (int argc, const char * argv[])
{
    NSAutoreleasePool * pool = [[NSAutoreleasePool alloc] init];
        unichar c;
        UInt32 valid = 0, invalid = 0;
        NSDate* startDate = [NSDate date];
        
        for (c = 0; c < 65535; c++)
        {
            #ifdef COCOA_WAY
                NSString *charString = [[NSString alloc] initWithCharacters:&c length:1];
                BOOL isValidChar = [charString canBeConvertedToEncoding:NSMacOSRomanStringEncoding];
                
                if(isValidChar) valid++;
                else invalid++;
                
                [charString release];
            #else
                UInt32 ucl, ubl;
                BOOL isNotValidChar = CFStringEncodingUnicodeToBytes(kCFStringEncodingMacRoman,  (1 << 6), &c, 1, &ucl, NULL, 0, &ubl);
                
                if(isNotValidChar) invalid++;
                else valid++;
            #endif
        }
        
        NSDate* stopDate = [NSDate date];
        NSTimeInterval interval = [stopDate timeIntervalSinceDate:startDate];
        NSLog(@"stop Date: %@\ntime to execute: %f secs", stopDate, interval);
        NSLog(@"number of valid chars: %d\nnumber of invalid chars: %d", valid, invalid);
    [pool release];
    return 0;
}


----

Man.  I do love my Tibook but it's sure not the fastest boat in the pond.  The other way to do this would be to create a buffer with all the characters in the encoding, make an NSString from it, create an NSCharacterSet using the +characterSetWithCharactersInString: method and then just testing for membership in the character set.  Obviously, this would only be easy for fixed-length encodings like ASCII, ISO Latin-1 and Mac Roman, but it would probably be significantly faster.  -- Bo

----

Well, maybe, but the following is working fine. THX!: -- JP

    
static inline bool IsUniCharAvailable(NSStringEncoding encoding, UniChar ch)
{
    NSString *charString = [[NSString allocWithZone:NULL] initWithCharacters:&ch length:1];
    BOOL isValid = [charString canBeConvertedToEncoding:encoding];
    [charString release];
    return isValid;
}

