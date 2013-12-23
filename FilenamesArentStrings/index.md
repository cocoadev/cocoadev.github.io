---
layout: page
title: FilenamesArentStrings
---



I recently added drag-and-drop to an app so that a user can drag a list of files onto the app's icon. I found though that apparently when drag-and-drop is used, files don't come in order as they would if you used an NSOpenPanel - they come in pell-mell.

To remedy I did a      [NSArray sortedArrayUsingSelector:@selector(compare:)]  on the array of filenames sent in by      [NSApp application:(NSApplication *)sender openFiles:(NSArray *)filenames 

My understanding is that this could would use NSString's compare method to sort the array. However, the results don't match up with what the Finder displays, though they mostly do. For example,

Desktop/Foo/Foo_100_v13_c114[Yanime_AW] 

was decided to be before 

Desktop/Foo/foo_100_v11_ch094 [Yanime_A-W]

by the code, when the Finder says the opposite. Is there a way to remedy this?

----
To sort NSString<nowiki/>s "numerically" (like the Finder) you need to use     compare:options: and the     NSNumericSearch option. Unfortunately,     sortedArrayUsingSelector: can't take any other arguments, so you will have to define a category on NSString with some sort of     numericCompare: that passes the message along. --JediKnil

*You'll also want that compare to be case-insensitive, I think.*

----

Bleh. I've never done a category before, but I whipped up NumericOption.h & NumericOption.m real quick.

NumericOption.h
    
#import <Foundation/NSString.h>


@interface NSString (NumericOption)

-(NSComparisonResult)numericCompare:(NSString *)aString;

@end


NumericOption.m
    
#import "Foundation/Foundation.h"
#import "NumericOption.h"


@implementation NSString (NumericOption)

-(NSComparisonResult)numericCompare:(NSString *)aString
{
	return [self compare:aString options:NSNumericSearch | NSCaseInsensitiveSearch];
}

@end


The compiler is telling me there's a parse error before '{' token on the NumericOption.m line that says '#import "NumericOption.h". I dunno why. I've been staring at but I get the feeling I'm missing something obvious.

*Very simple. Categories are not allowed to add instance variables to the class, so there isn't a section for them. Just take out both braces and you're OK. Well, almost; the operator used to combine NSNumericSearch and NSCaseInsensitiveSearch should be     |.     || yields a boolean value, instead of an integer. --JediKnil*

*
I have modified the above code to fix the ||.
-- RandomThirdParty
*

**
Modified to remove the extraneous ivar section. -- EmanueleVulcano aka l0ne
**

----

If you want to be Finder-like, you don't have everything you need yet.  You also want to be doing a localized compare with respect to your current locale.  (See     -[NSString compare:options:range:locale:]).  'Alphabetical order' is something that is language and locale dependent.  For example, for Swedes, '�' is a letter that comes at the end of the alphabet.  Americans would expect it to come after 'a' and before 'b'.

*You're referring to norweigans and danes, we swedes use "�", and it's a pain when it sorts with "a" since they are in the opposite ends of the alphabet. =)*

----

The finder uses UCCompareTextDefault to do text comparisons. Here is some code for a compare function that can be adopted to do what you want. In this case, I needed to sort objects that represented files on disk; you could adapt this code to be a NSString category with some trivial changes. The code was adopted from the developer QA at http://developer.apple.com/qa/qa2004/qa1159.html

    
const UCCollateOptions MLIMAGE_COMPARE_OPTIONS =
kUCCollateComposeInsensitiveMask
| kUCCollateWidthInsensitiveMask
| kUCCollateCaseInsensitiveMask
| kUCCollateDigitsOverrideMask
| kUCCollateDigitsAsNumberMask
| kUCCollatePunctuationSignificantMask;

- (NSComparisonResult) finderCompare:(MLImage *)otherImage {
	SInt32 compareResult;
	NSString *path1;
	NSString *path2;
	UniChar buff1[MAXPATHLEN];
	UniChar buff2[MAXPATHLEN];
	
	path1 = [self path];
	path2 = [otherImage path];
	
	[path1 getCharacters:buff1];
	[path2 getCharacters:buff2];
	
	UCCompareTextDefault(MLIMAGE_COMPARE_OPTIONS, buff1, [path1 length], buff2, [path2 length], NULL, &compareResult);
	
	return((NSComparisonResult)compareResult);	
}


-- AllanHsu

**
I'm not in a situation where I can check right now, but I think the above has a bug.  The contents of the two buffers, buff1 and buff2 will start with the ByteOrderMark.  That character is not considered in [path1 length] or [path2 length] - the upshot is that buff1 and buff2 are one character longer than UCCompareTextDefault will think they are.  

To test this hypothesis, try comparing one character strings.  My prediction is that     [@"a" finderCompare:@"b"] will return     NSOrderedSame.
**

This is kind of an old entry, but I checked back in here and I noticed the text above. [NSString getCharacters:] does not generate a BOM. If you read the NSString API documentation, getCharacters: calls getCharacters:range:, which in the abstract implementation, just calls characterAtIndex: repeatedly. The characters that end up in the buffer are in native byte-order.

-AllanHsu

----

I made a category for NSString to compare two objects using USCompareTextDefault, here's my code:

    
#import "FinderCompare.h"

@implementation NSString (FinderCompare)

- (NSComparisonResult)finderCompare:(NSString *)aString
{
	SInt32 compareResult;
	
	CFIndex lhsLen = [self length];;
    CFIndex rhsLen = [aString length];
	
	UniChar *lhsBuf = malloc(lhsLen * sizeof(UniChar));
	UniChar *rhsBuf = malloc(rhsLen * sizeof(UniChar));
	
	[self getCharacters:lhsBuf];
	[aString getCharacters:rhsBuf];
	
	(void) UCCompareTextDefault(kUCCollateComposeInsensitiveMask | kUCCollateWidthInsensitiveMask | kUCCollateCaseInsensitiveMask | kUCCollateDigitsOverrideMask | kUCCollateDigitsAsNumberMask| kUCCollatePunctuationSignificantMask,lhsBuf,lhsLen,rhsBuf,rhsLen,NULL,&compareResult);
	
	free(lhsBuf);
	free(rhsBuf);
	
	return (CFComparisonResult) compareResult;
}

@end


I'm allocating the buffers according to the string's size rather than simple using the MAXPATHLEN one, which is supposed to be used for paths only. -PabloGomez

*It's faster to use stack memory like in the first sample than allocated heap memory via malloc function. The first way was better IMHO.*

It's only better *when it's correct*! Paths are *not* guaranteed to be less than MAXPATHLEN, and so allocating memory on the stack like that is wrong. You must either use dynamic stack allocation (shudder) or regular dynamic heap allocation.

Usable paths are, indeed, guaranteed to be less than MAXPATHLEN (which is 4096 bytes on OSX). While it's true that HFS+ and many other filesystems don't have a built-in path limit, the kernel, libraries, and frameworks all heavily rely on buffers of size PATH_MAX = MAXPATHLEN. If you've ever tried to make path that's longer (yes, I have!) you'll find all kinds of interesting failures in shell tools and the Finder. With all that said, however, the first code snippet doesn't do *any* error-checking on the incoming strings or even bother to check their length -- so it's vulnerable to buffer overflows depending on where the incoming strings are from. Either add additional error-checking to the first one, or use Pablo's version ... in practice I doubt there will be a noticeable speed difference. --DrewThaler

