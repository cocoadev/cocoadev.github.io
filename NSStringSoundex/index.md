---
layout: page
title: NSStringSoundex
---

This is a simple category on NSString to allow comparisons using the Soundex algorithm. This compares a string by the way it sounds, i.e. phonetically. It can be quite handy for suggesting alternative words for example, or doing fuzzy matches, book indexing, etc. GrahamCox.

    

#import <Cocoa/Cocoa.h>


@interface NSString (Soundex)

- (NSString*)	soundexString;
- (BOOL)	soundsLikeString:(NSString*) aString;


@end

#import "NSString+Soundex.h"

@implementation NSString (Soundex)


static NSArray* soundexCharSets = nil;

- (void)		initSoundex
{
	if( soundexCharSets == nil )
	{
		NSMutableArray* cs = [NSMutableArray array];
		NSCharacterSet* charSet;
		
		charSet = [NSCharacterSet characterSetWithCharactersInString:@"aeiouhw"];
		[cs addObject:charSet];
		charSet = [NSCharacterSet characterSetWithCharactersInString:@"bfpv"];
		[cs addObject:charSet];
		charSet = [NSCharacterSet characterSetWithCharactersInString:@"cgjkqsxz"];
		[cs addObject:charSet];
		charSet = [NSCharacterSet characterSetWithCharactersInString:@"dt"];
		[cs addObject:charSet];
		charSet = [NSCharacterSet characterSetWithCharactersInString:@"l"];
		[cs addObject:charSet];
		charSet = [NSCharacterSet characterSetWithCharactersInString:@"mn"];
		[cs addObject:charSet];
		charSet = [NSCharacterSet characterSetWithCharactersInString:@"r"];
		[cs addObject:charSet];
		
		soundexCharSets = [cs retain];
	}
}


- (NSString *)	stringByRemovingCharactersInSet:(NSCharacterSet*) charSet options:(unsigned) mask
{
	NSRange				range;
	NSMutableString*	newString = [NSMutableString string];
	unsigned			len = [self length];
	
	mask &= ~NSBackwardsSearch;
	range = NSMakeRange (0, len);
	while (range.length)
	{
		NSRange substringRange;
		unsigned pos = range.location;
		
		range = [self rangeOfCharacterFromSet:charSet options:mask range:range];
		if (range.location == NSNotFound)
			range = NSMakeRange (len, 0);
		
		substringRange = NSMakeRange (pos, range.location - pos);
		[newString appendString:[self substringWithRange:substringRange]];
		
		range.location += range.length;
		range.length = len - range.location;
	}
	
	return newString;
}


- (NSString *)	stringByRemovingCharactersInSet:(NSCharacterSet*) charSet
{
	return [self stringByRemovingCharactersInSet:charSet options:0];
}


- (unsigned)	soundexValueForCharacter:(unichar) aCharacter
{
	// returns the soundex mapping for the first character in the string. If the value returned is 0, the character should be discarded.
	
	unsigned		indx;
	NSCharacterSet* cs;
	
	for( indx = 0; indx < [soundexCharSets count]; ++indx )
	{
		cs = [soundexCharSets objectAtIndex:indx];
		
		if([cs characterIsMember:aCharacter])
			return indx;
	}
	
	return 0;
}


- (NSString*)	soundexString
{
	// returns the Soundex representation of the string. 
	/*
	 
	 Replace consonants with digits as follows (but do not change the first letter):
	 b, f, p, v => 1
	 c, g, j, k, q, s, x, z => 2
	 d, t => 3
	 l => 4
	 m, n => 5
	 r => 6
	 Collapse adjacent identical digits into a single digit of that value.
	 Remove all non-digits after the first letter.
	 Return the starting letter and the first three remaining digits. If needed, append zeroes to make it a letter and three digits.
	 
	 */
	
	[self initSoundex];
	
	if([self length] > 0)
	{
		NSMutableString* soundexStr = [NSMutableString string];
		
		// strip whitespace and convert to lower case
		
		NSString*	workingString = self lowercaseString] stringByRemovingCharactersInSet:[[[NSCharacterSet whitespaceAndNewlineCharacterSet]];
		unsigned	indx, soundValue, previousSoundValue = 0;
		
		// include first character
		
		[soundexStr appendString:[workingString substringToIndex:1]];
		
		// convert up to 3 more significant characters
		
		for( indx = 1; indx < [workingString length]; ++indx )
		{
			soundValue = [self soundexValueForCharacter:[workingString characterAtIndex:indx]];
			
			if( soundValue > 0 && soundValue != previousSoundValue )
				[soundexStr appendString:[NSString stringWithFormat:@"%d", soundValue]];
				
			previousSoundValue = soundValue;	
			
			// if we've got four characters, don't need to scan any more
			
			if([soundexStr length] >= 4)
				break;
		}
		
		// if < 4 characters, need to pad the string with zeroes
		
		while([soundexStr length] < 4)
			[soundexStr appendString:@"0"];
		
		//NSLog(@"soundex for '%@' = %@", self, soundexStr );
		
		return soundexStr;
	}
	else
		return @"";
}


- (BOOL)		soundsLikeString:(NSString*) aString
{
	return self soundexString] isEqualToString:[aString soundexString;
}

@end


