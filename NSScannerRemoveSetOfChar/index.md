---
layout: page
title: NSScannerRemoveSetOfChar
---

I want to remove all is not Decimal on my NSString @"v2.4.2"

I try with NScanner some thing like this :

    
UInt16	currentVersionInt = -1;
NSScanner *scanVersion = [NSScanner scannerWithString:[[[NSBundle mainBundle] infoDictionary] objectForKey:@"CFBundleVersion"]];	
[scanVersion setCharactersToBeSkipped:[[NSCharacterSet decimalDigitCharacterSet] invertedSet]];	
[scanVersion scanInt:&currentVersionInt];

but it's not work ... why ?

thanks for your help.

----
I make a category of NSString like this...

NSString+Filter.h:
    
#import <Cocoa/Cocoa.h>

/* Filter category for NSString: make a NSString with only set of characters you want.

	sample to extract '204' of current app version 'v2.0.4'  :
	NSString *currentVersionString = [NSString stringWithString:[[[NSBundle mainBundle] infoDictionary] objectForKey:@"CFBundleVersion"]
													  andFilter:[NSCharacterSet decimalDigitCharacterSet]];

 */

@interface NSString (Filter)
+ (NSString *) stringWithString:(NSString *)string andFilter:(NSCharacterSet *)set;
@end




NSString+Filter.m:
    
#import <Cocoa/Cocoa.h>
#import "NSString+Filter.h"

@implementation NSString (Filter)
+ (NSString *) stringWithString:(NSString *)string andFilter:(NSCharacterSet *)set
{
	//---- result in autorelease...
	NSMutableString *result = [NSMutableString stringWithString:@""];

	NSScanner *scanner = [NSScanner scannerWithString:string];
	while(NO == [scanner isAtEnd]) {
		unsigned position;
		NSString *goodString;
		
		//---- search for good string
		if([scanner scanCharactersFromSet:set
							   intoString:&goodString]) {
			[result appendString:goodString];
		}
		
		//---- one step if it's possible !
		if ([scanner scanLocation] < [string  length]) {
			[scanner setScanLocation:[scanner scanLocation] + 1];
			position = [scanner scanLocation];
		}
	}
	
	return (NSString *)result;
}

@end


The above code is placed in the public domain if anyone cares to use it.

- JMM Code Park
http://jm.marino.free.fr

---- Despite the common belief, "placing code in the public domain" has no legal meaning. Instead, you must agree to waive your right to sue for copyright infringement. Try the MIT license, for instance - http://www.opensource.org/licenses/mit-license.php

----

This is something that I've seen asserted repeatedly, but never saw any proof. Wikipedia disagrees with you:

*It is commonly believed by non-lawyers that it is impossible to put a work into the public domain. Although copyright law generally does not provide any statutory means to "abandon" copyright so that a work can enter the public domain, this does not mean that it is impossible or even difficult, only that the law is somewhat unclear. Congress may not have felt it necessary to codify this part of the law, because abandoning property (like a tract of land) to the public domain has traditionally been a matter of judge-made law, rather than statute.*

If you have some actual evidence to the contrary, then I'd like to see it. But I find the idea that you have to use an *explicit* license, and that saying "you can use this for whatever you want" (which is what "this is in the public domain" means) doesn't actually work to be extremely silly. -- PrimeOperator

