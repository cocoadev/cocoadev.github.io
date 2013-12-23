---
layout: page
title: NSStringCategory
---




GormanChristian thought it would be a good idea to start a page with useful string methods. Now, anyone in the community can feel free to add a method or two (or more)! (Functions are also acceptable.) Refactoring is also always welcome.

*Be careful when using methods that return non-class types (such as int or NSRange). You'll need to #import the category's header file or declare the method some other way, or else the compiler will consider the return type to be id and complain*

**NSString category CocoaDevUsersAdditions:**

**NSString+CocoaDevUsersAdditions.h**
    
 #import <Foundation/Foundation.h>
 
 @interface NSString (CocoaDevUsersAdditions)
 + (NSString *)stringWithPascalString:(Str255)aString encoding:(CFStringEncoding)encoding;
 - (id)initWithPascalString:(Str255)aString encoding:(CFStringEncoding)encoding;
 - (int)convertToPascalStringInBuffer:(StringPtr)strBuffer
 size:(int)bufSize encoding:(CFStringEncoding)encoding;
 
 - (int)occurencesOfSubstring:(NSString *)substr;
 - (int)occurencesOfSubstring:(NSString *)substr options:(int)opt;
 
 - (NSArray *)tokensSeparatedByCharactersFromSet:(NSCharacterSet *) separatorSet;
 - (NSArray *)objCTokens; //(and C tokens too) a contiguous body of non-'punctuation' characters.
                               //skips quotes, -, +, (), {}, [], and the like.
 - (NSArray *)words; //roman-alphabet words
 
 - (BOOL) containsCharacterFromSet:(NSCharacterSet *)set;
 - (NSString *)stringWithSubstitute:(NSString *)subs forCharactersFromSet:(NSCharacterSet *)set;
 
 - (NSArray *)linesSortedByLength;
 - (NSComparisonResult)compareLength:(NSString *)otherString;
 
 - (BOOL)smartWriteToFile:(NSString *)path atomically:(BOOL)atomically;
 //won't write to file if nothing has changed.
 //(may be slow for large amounts of text)
 
 - (unsigned)lineCount; // count lines/paragraphs
 
 - (BOOL)containsString:(NSString *)aString;
 - (BOOL)containsString:(NSString *)aString ignoringCase:(BOOL)flag;
 
 -(NSArray *) splitToSize:(unsigned)size; //returns NSArray of <= size character strings
 -(NSString *)removeTabsAndReturns;
 -(NSString *)newlineToCR;
 
 -(NSString *)safeFilePath;
 
 -(NSRange)whitespaceRangeForRange:(NSRange) characterRange;
 //returns the range of characters around characterRange, extended out to the nearest whitespace
 
 - (NSString *)substringBeforeRange:(NSRange)range;
 - (NSString *)substringAfterRange:(NSRange)range;
 
 // returns an array of strings corresponding to matches to subexpressions within a regular expression
 - (NSArray *) findRegularExpression:(NSString *)re ignoreCase:(BOOL)ignoreCase;
 
 -(BOOL)isValidURL;
 - (NSString *)stringSafeForXML;
 
 -(OSStatus)pathToFSRef:(FSRef*)ref;
 +(NSString*)pathFromFSRef:(FSRef*)ref;
 
 @end



**NSString+CocoaDevUsersAdditions.m**
    
 #import "NSString+CocoaDevUsersAdditions.h"
 
 @implementation NSString (CocoaDevUsersAdditions)
 
 + (NSString *)stringWithPascalString:(Str255)aString encoding:(CFStringEncoding)encoding {
   return [self alloc] initWithPascalString:aString encoding:encoding] autorelease];
 }
 
 - (id)initWithPascalString:(Str255)aString encoding:(CFStringEncoding)encoding {
   id string = nil;
   CFStringRef cfstring = CFStringCreateWithPascalString (NULL, aString, encoding);
   if (cfstring) {
     string = [self initWithString:(NSString*)cfstring];
     CFRelease (cfstring); // Calls "abort" if null.
   }
   return string;
 }
 
 // Returns size of string, including length byte (i.e. conversion to "hi" returns 3), or 0 if failed.
 - (int)convertToPascalStringInBuffer:(StringPtr)strBuffer
                                 size:(int)bufSize encoding:(CFStringEncoding)encoding {
   Boolean success = CFStringGetPascalString ((CFStringRef)self, strBuffer, bufSize, encoding);
   return (success ? (*((const unsigned char*) strBuffer) + 1) : 0);
 }
 
 -(int)occurencesOfSubstring:(NSString *)substr {
   //consider that for this, it may be(though I'm not sure) faster to use the less-robust outlined below.
   return [self occurencesOfSubstring:substr options:NSCaseInsensitiveSearch];
 }
 
 -(int)occurencesOfSubstring:(NSString *)substr options:(int)opt {
   /* if one wanted to make a much shorter(is it faster?), but rather less robust implementation, one would do:
    return [[self componentsSeparatedByString:substr] count] - 1; */
   int strlen = [self length];
   int position = 0;
   NSRange currentRange;
   BOOL flag = YES;
   int count = 0;
   do {
     currentRange = [self rangeOfString:substr
                                options:opt
                                  range:NSMakeRange(position,
                                                        strlen-position)];
     if (currentRange.location == NSNotFound) {
       flag = NO;
     } else {
       count++;
       position = currentRange.location + currentRange.length;
     }
   } while (flag == YES);
   
   return count;
 }
 
 - (NSArray *)tokensSeparatedByCharactersFromSet:(NSCharacterSet *)separatorSet
 {
   NSScanner      *scanner      = [NSScanner scannerWithString:self];
   NSCharacterSet *tokenSet     = [separatorSet invertedSet];
   NSMutableArray *tokens       = [NSMutableArray array];
   
   [scanner setCharactersToBeSkipped:separatorSet];
   
   while (![scanner isAtEnd])
   {
     NSString  *destination = [NSString string];
     
     if ([scanner scanCharactersFromSet:tokenSet intoString:&destination])
     {
       [tokens addObject:[NSString stringWithString:destination;
     }
   }
   
   return [NSArray arrayWithArray:tokens];
 }
 
 - (NSArray *)objCTokens
 {
   NSMutableCharacterSet *tokensSet = [NSMutableCharacterSet alphanumericCharacterSet];
   [tokensSet addCharactersInString:@"_:"];
   return [self tokensSeparatedByCharactersFromSet:[tokensSet invertedSet]];
 }
 
 //be careful if you're using SenTe's SenFoundation.  It implements -words as well,
 //and you may become confused in seeking bugs.
 
 //NSTextStorage implements a -words method as well (in 10.3, at least). You may be able to use that instead.
 - (NSArray *)words
 {
   NSMutableCharacterSet *tokenSet = [NSMutableCharacterSet letterCharacterSet];
   [tokenSet addCharactersInString:@"-"];
   return [self tokensSeparatedByCharactersFromSet:[tokenSet invertedSet]];
 }
 
 // Useful for checking for illegal characters.
 - (BOOL) containsCharacterFromSet:(NSCharacterSet *)set
 {
   return ([self rangeOfCharacterFromSet:set].location != NSNotFound);
 }
 
 // Useful for replacing illegal characters with "?" or something.
 - (NSString *)stringWithSubstitute:(NSString *)subs forCharactersFromSet:(NSCharacterSet *)set
 {
   NSRange r = [self rangeOfCharacterFromSet:set];
   if (r.location == NSNotFound) return self;
   NSMutableString *newString = [self mutableCopy];
   do
   {
     [newString replaceCharactersInRange:r withString:subs];
     r = [newString rangeOfCharacterFromSet:set];
   }
   while (r.location != NSNotFound);
   return [newString autorelease];
 }
 
 -(NSArray *)linesSortedByLength {
   return self componentsSeparatedByString:@"\n"] sortedArrayUsingSelector:@selector(compareLength:)];
 }
 
 -(NSComparisonResult)compareLength:(NSString *)otherString {
   if([self length] < [otherString length])      { return NSOrderedAscending; }
   else if([self length] > [otherString length]) { return NSOrderedDescending; }
   //if same length, use alphabetical ordering.
   else                                          { return [self compare:otherString]; }
 }
 
 - (BOOL)smartWriteToFile:(NSString *)path atomically:(BOOL)atomically
 {
   if([self isEqualToString:[NSString stringWithContentsOfFile:path) { return YES; }
   return [self writeToFile:path atomically:atomically];
 }
 
 - (unsigned)lineCount
 {
   unsigned count = 0;
   unsigned location = 0;
   
   while (location < [self length])
   {
     // get next line start and set current location to it
     [self getLineStart:nil end:&location contentsEnd:nil forRange:NSMakeRange(location,1)];
     count += 1;
   }
   
   return count;
 }
 
 - (BOOL)containsString:(NSString *)aString
 {
   return [self containsString:aString ignoringCase:NO];
 }
 
 - (BOOL)containsString:(NSString *)aString ignoringCase:(BOOL)flag
 {
   unsigned mask = (flag ? NSCaseInsensitiveSearch : 0);
   NSRange range = [self rangeOfString:aString options:mask];
   return (range.length > 0);
 }
 
 -(NSArray *) splitToSize:(unsigned)size
 {
   NSMutableArray *splitStrings = [NSMutableArray array];
   
   int count = 0;
   int i = 0;
   unsigned loc = 0;
   NSString *tempString;
   
   count = [self length] / size;
   
   
   for (i=0; i < count; i++)
   {
     loc = size * i;
     
     tempString = [self substringWithRange:NSMakeRange(loc,size)];
     [splitStrings addObject: [tempString copy]];
   }
   
   loc = size * count;
   
   tempString = [self substringFromIndex:loc];
   
   [splitStrings addObject: [tempString copy]];
   
   return splitStrings;
 }
 
 -(NSString *)removeTabsAndReturns
 {
   NSMutableString *outputString = [NSMutableString string];
   NSCharacterSet *charSet;
   NSString *temp;
   
   NSScanner *scanner = [NSScanner scannerWithString:self];
   
   charSet = [NSCharacterSet characterSetWithCharactersInString:@"\n\r\t"];
   
   while ([scanner scanUpToCharactersFromSet:charSet intoString:&temp])
   {
  	[outputString appendString:temp];
     
   }
   return outputString copy] autorelease];
 }
 
 -(NSString*)newlineToCR
 {
   NSMutableString *str = [NSMutableString string];
   [str setString: self];
   
   [str replaceOccurrencesOfString:@"\n" withString:@"\r"
                           options:NSLiteralSearch
                             range:NSMakeRange (0, [str length])];
   return [[str copy] autorelease];
 }
 
 -(NSString *)safeFilePath
 {
   int numberWithName = 1;
   BOOL isDir;
   NSString *safePath = [[NSString alloc] initWithString:self];
   
   if ([[NSFileManager defaultManager] fileExistsAtPath:safePath
                                                isDirectory:&isDir])
   {
     while ([[NSFileManager defaultManager] fileExistsAtPath:safePath
                                                     isDirectory:&isDir])
     {
       [safePath release];
       safePath = [[NSString alloc] initWithFormat:@"%@ %d.%@",
                   [self       stringByDeletingPathExtension],
                   numberWithName,[self pathExtension;
       
       numberWithName++;
     }
   }
   
   return safePath;
 }
 
 -(NSRange)whitespaceRangeForRange:(NSRange)characterRange
 {
   NSString *string = self copy] autorelease];
   NSCharacterSet *whitespaceSet = [NSCharacterSet whitespaceAndNewlineCharacterSet];
   unsigned int areamax = NSMaxRange(characterRange);
   unsigned int length = [string length];
   
   NSRange start = [string rangeOfCharacterFromSet:whitespaceSet
                                               options:NSBackwardsSearch
                                                 range:NSMakeRange(0, characterRange.location)];
   
   if (start.location == NSNotFound)
   {
     start.location = 0;
   }
   else
   {
     start.location = NSMaxRange(start);
   }
   
   NSRange end = [string rangeOfCharacterFromSet:whitespaceSet
                                             options:0
                                               range:NSMakeRange(areamax, length - areamax)];
   
   if (end.location == NSNotFound)
     end.location = length;
   
   NSRange searchRange = NSMakeRange(start.location, end.location - start.location);
   //last whitespace to next whitespace
   return searchRange;
 }
 
 // Useful for parsing strings, in conjunction with rangeOf... methods.
 - (NSString *)substringBeforeRange:(NSRange)range
 {
   return [self substringToIndex:range.location];
 }
 
 - (NSString *)substringAfterRange:(NSRange)range
 {
   return [self substringFromIndex:NSMaxRange (range)];
 }
 
 #include <regex.h>
 
 /*
  * - (NSArray *) findRegularExpression:(NSString *)re ignoreCase:(BOOL)ignoreCase;
  *
  * Apply the given regular expression (see re_format(7)) to the string and return an array of
  * substrings corresponding to any matched parenthesized subexpressions.  Note that the first
  * element of the returned array is the substring matched by the entire regular expression,
  * element 1 is the match for the first subexpression, and so on.  [NSNull null] is returned
  * for elements that are not matched.
  *
  * Sample usage:
  *
  * NSString *testStr = [NSString stringWithString:@"<?xml version=\"1.0\" encoding=\"UTF-16\"?>"];
  * NSArray *matches = [testStr findRegularExpression:@"version=\"([^\"]+)+.*encoding=\"([^\"]+)+" ignoreCase:YES];
  * NSLog(@"matches: %@", matches);
  *
  * Result:
  *
  * matches: <CFArray 0x134d540 [0xa01900e0]>{type = mutable-small, count = 3, values = (
  *      0 : <CFString 0x134d910 [0xa01900e0]>{contents = "version="1.0" encoding="UTF-16"}
  *      1 : <CFString 0x134d940 [0xa01900e0]>{contents = "1.0"}
  *      2 : <CFString 0x134d950 [0xa01900e0]>{contents = "UTF-16"}
  * )}
  */
 
 static void raise_reg_error_exception(const char* name, int errorcode, regex_t* regex)
 {
   NSMutableData*  errorString = [NSMutableData data];
   
   [errorString setLength:regerror(errorcode, regex, NULL, 0)];
   
   size_t errorSize = regerror(errorcode, regex, [errorString mutableBytes], [errorString length]);
   
   regfree(regex);
   
   NSCAssert2(errorSize == [errorString length],
              @"Unexpected size in raise_reg_error_exception: %d, %d",
              errorSize, [errorString length]);
   
   [NSException raise: NSInvalidArgumentException
                   format: @"%s: %s", name, [errorString bytes;
 }
 
 - (NSArray *) findRegularExpression:(NSString *)re ignoreCase:(BOOL)ignoreCase;
 {
   regex_t regex;
   int regCompFlags = REG_EXTENDED;
   int regExecFlags = 0;
   int err = 0;
   
   //NSLog(@"@\"%@\": findRegularExpression:@\"%@\" ignoreCase:%s", self, re, (ignoreCase ? "YES" : "NO"));
   
   if (ignoreCase)
   {
     regCompFlags |= REG_ICASE;
   }
   
   // Compile the regular expression
   if ((err = regcomp(&regex, [re UTF8String], regCompFlags)) != 0)
   {
     // Failed to compile the RE, issue a diagnostic message
     raise_reg_error_exception("regcomp", err, &regex);
     return nil;
   }
   
   //NSLog(@"match count: %d", 1 + regex.re_nsub);
   
   NSMutableData*  matchData = [NSMutableData dataWithCapacity: (1 + regex.re_nsub) * sizeof (regmatch_t)];
   regmatch_t*     matches   = [matchData mutableBytes];
   
   // Execute the compiled regular expression
   if ((err = regexec(&regex, [self UTF8String], 1 + regex.re_nsub, matches, regExecFlags)) != 0)
   {
     if (err != REG_NOMATCH)
     {
       raise_reg_error_exception("regexec", err, &regex);
     }
     
     // The "nil" return indicates there was no match.  No need for a diagnostic.
     regfree(&regex);
     return nil;
   }
   
   // Place the matches in an array and return them
   NSMutableArray *matchArray = [NSMutableArray arrayWithCapacity: 1 + regex.re_nsub];
   int m;
   
   for (m = 0; m <= regex.re_nsub; m++)
   {
     if (matches[m].rm_so == -1)
     {
       [matchArray addObject: [NSNull null]];
     }
     else
     {
       NSRange matchRange = NSMakeRange(matches[m].rm_so, (matches[m].rm_eo - matches[m].rm_so));
       
       [matchArray addObject:[self substringWithRange:matchRange]];
     }
   }
   
   regfree(&regex);
   
   return matchArray;
 }
 
 -(BOOL)isValidURL
 {
   return ([NSURL URLWithString:self] != nil);
 }
 
 // Replaces all XML/HTML reserved chars with entities.
 - (NSString *)stringSafeForXML
 {
   NSMutableString *str = [NSMutableString stringWithString:self];
   NSRange all = NSMakeRange (0, [str length]);
   [str replaceOccurrencesOfString:@"&" withString:@"&amp;" options:NSLiteralSearch range:all];
   [str replaceOccurrencesOfString:@"<" withString:@"&lt;" options:NSLiteralSearch range:all];
   [str replaceOccurrencesOfString:@">" withString:@"&gt;" options:NSLiteralSearch range:all];
   [str replaceOccurrencesOfString:@"\"" withString:@"&quot;" options:NSLiteralSearch range:all];
   [str replaceOccurrencesOfString:@"'" withString:@"&apos;" options:NSLiteralSearch range:all];
   return str;
 }
 
 -(OSStatus)pathToFSRef:(FSRef*)ref
 {
   return( FSPathMakeRef( (const UInt8 *)[self fileSystemRepresentation], ref, NULL ) );
 }
 
 +(NSString*)pathFromFSRef:(FSRef*)ref
 {
   NSString	*path = nil;
   CFURLRef	url = CFURLCreateFromFSRef( kCFAllocatorDefault, ref );
   if ( url != NULL ) {
     path = (NSString *)CFURLCopyFileSystemPath( url, kCFURLPOSIXPathStyle );
     [path autorelease];
     CFRelease( url );
   }
   
   return( path );
 }
 
 @end


----
**NSString category CocoaDevUsersAdditionsTest using ObjcUnit framework:**

**NSString+CocoaDevUsersAdditionsTest.h**
    
 #import <ObjcUnit/ObjcUnit.h>
 
 @interface NSString+CocoaDevUsersAdditionsTest: TestCase {
 }
 
 @end


**NSString+CocoaDevUsersAdditionsTest.m**
    
 #import "NSString+CocoaDevUsersAdditionsTest.h"
 #import "NSString+CocoaDevUsersAdditions.h"
 
 
 @implementation NSString+CocoaDevUsersAdditionsTest
 
 - (void)setUp
 {
 }
 
 - (void)tearDown
 {
 }
 
 - (void)testLineCount
 {
   // empty string
   [self assertInt:[@"" lineCount] equals:0];
   
   // one line
   [self assertInt:[@"\n" lineCount] equals:1];
   [self assertInt:[@"First" lineCount] equals:1];
   [self assertInt:[@"First\n" lineCount] equals:1];
   
   // multiply lines
   [self assertInt:[@"\nFirst\n" lineCount] equals:2];
   [self assertInt:[@"First\nSecond" lineCount] equals:2];
   [self assertInt:[@"First\nSecond\n" lineCount] equals:2];
   
   // empty lines between
   [self assertInt:[@"First\n\nSecond\n" lineCount] equals:3];
 }
 
 @end


----
**Revision History**

Adjusted indentation, spacing, braces. -- DustinVoss

Added header and implemetation files for unit tesing using ObjcUnit. I think it should be a part of any code. -- NirSoffer

Added someone's **containsString:**. -- DustinVoss

7 November 2003 - added splitToSize:(unsigned)size, removeTabsAndReturns, newlineToCR methods

**25 November 2003** - added GormanChristian's safeFilePath method from the SafelyNamingFiles page

**7 June 2004** - added     whitespaceRangeForRange: adapted from code by MikeTrent over at ImplementSyntaxHighlighting


**13 June 2004** - added     findRegularExpression: -- PerryClarke

**16 March 2005** - added     -(BOOL)isValidURL

**16 March 2005** - added     containsCharacterFromSet:,     stringWithSubstitute:forCharactersFromSet:,     stringSafeForXML, and     substringBefore/    AfterRange: -- DustinVoss

**9 October 2005** - cleaned up and modified -tokensSeparatedByCharactersFromSet: to behave as expected, that is, to return tokens separated by characters from specified set, instead of tokens separated by the inverse of the specified set.  Updated -words and -objCTokens accordingly and fixed compile error in -words, though I question the usefulness of these methods.  Corrected misuse of NULL in containsString: and eliminated C99 scoping in *for* loop within -findRegularExpression:ignoreCase: to eliminate compiler warnings.  Fixed bug in findRegularExpression:ignoreCase: where *for* loop would be exited prematurely if a subexpression was not matched; instead, now returns [NSNull null] in this case.  Also rewrote this method to use NSMutableData, removing hard-coded space allocation, and added raise_reg_error_exception function to raise an exception on error.  -- TkM

----

**NSString (rot13)**

This is a simple category to add a class function that allows ROT13 encoding to the string class. 
A friend asked for a simple ROT13 encoder/decoder, so I hacked this together real quick while 
having my morning coffee. I am sure I could probably have written this a touch cleaner, but as 
of now it works and is compliant with ROT13 standards, and most of all, it's easy to use. 

**Example:**
    
 NSString *rotted = [NSString rot13:@"This text is encoded in ROT13 format"];


The same function encoded and decodes, so it's easy to use.


**NSString+rot13.h**
    
 //
 //  rot13.h
 //  iROT13
 //
 //  Created by David Giffin on 11/9/04.
 //  Copyright 2004 David Giffin <http://www.davtri.com>. All rights reserved.
 //
 
 #import <Foundation/NSString.h>
 
 @interface NSString (rot13)
 + (NSString *)rot13:(NSString *)theText;
 @end


**NSString+rot13.m**
    
 //
 //  rot13.m
 //  iROT13
 //
 //  Created by David Giffin on 11/9/04.
 //  Copyright 2004 David Giffin <http://www.davtri.com>. All rights reserved.
 //
 
 #import "NSString+rot13.h"
 
 
 @implementation NSString (rot13)
 
 + (NSString *)rot13:(NSString *)theText {
   NSMutableString *holder = NSMutableString alloc] init];
   unichar theChar;
   int i;
   
   for(i = 0; i < [theText length]; i++) {
     theChar = [theText characterAtIndex:i];
     if(theChar <= 122 && theChar >= 97) {
       if(theChar + 13 > 122)
         theChar -= 13;
       else
         theChar += 13;
       [holder appendFormat:@"%C", (char)theChar];
       
       
     } else if(theChar <= 90 && theChar >= 65) {
       if((int)theChar + 13 > 90)
         theChar -= 13;
       else
         theChar += 13;
       
       [holder appendFormat:@"%C", theChar];
       
     } else {
       [holder appendFormat:@"%C", theChar];
     }
   }
   
   return [NSString stringWithString:holder];
 }
 
 @end


I hope others find this of use... I have had to do this a few times over the years when some project called for ROT13, 
so I figured this way, others can get the use of this, and I can look it up here later in case I look the original (again).

[[DaveGiffin

*This code will break with characters that don't fit within a single byte, which is all unicode characters that are outside of the Latin1 charset. It will work fine for ASCII, but it will mangle general unicode text. Declaring theChar as a unichar and changing the append lines would fix it.*

*Thanks, I totally missed that. Still not used to messing with the unichar typedef... I just fixed this in the application and am updating the above posted code to reflect it. Again, thanks for the input.*
DaveGiffin

*Great! I'm always keeping my eye out for things like that. I'm glad you were able to fix it so easily. It does make one wonder, what exactly is the proper way to rot13 a bunch of Chinese text? :-)*

**It's probably like rot3000 or something...**

By the way, you are leaking the local NSMutableString holder.
    
 NSString *rval = [NSString stringWithString:holder];
 [holder release];
 return rval;


Also stylistically, and performance wise, you would be best to burn another local variable to cache [theText length] so you don't recompute it every time through the loop.
    
 NSUInteger len = [theText length];
 for(i = 0; i < len; i++) {

----

Added pathToFSRef and pathFromFSRef.

Usage:

    
 FSRef ref = {'\0'};			
 if ( [@"/System" pathToFSRef:&ref] == noErr )
 {
   NSString *path;
   path = [NSString pathFromFSRef:&ref];
   NSLog(path);
 }



Category:CocoaDevUsersAdditions

